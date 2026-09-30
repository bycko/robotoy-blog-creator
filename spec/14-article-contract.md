# 14 — Article contract

This is what Creator hands over, what Translator produces per language, and what Reviewer checks before the write. Every field that reaches the page is named here with the page field it lands in. What the page fields are and how they render is in [`11-storefront-data.md`](11-storefront-data.md); how the article reads is in [`09-editorial-guidelines.md`](09-editorial-guidelines.md); the widgets are in [`15-widgets.md`](15-widgets.md).

## Files

One article is one JSON file per language in the run directory ([`../runs/README.md`](../runs/README.md)).

| File | Who writes it | Locale |
|---|---|---|
| `runs/<run_id>/article.json` | Creator, overwritten on each revision | `sk` |
| `runs/<run_id>/translations/<locale>.json` | Translator, one per locale | the 20 other locales |
| `runs/<run_id>/cover.png` or `cover.jpg` | Creator | shared by all locales |

Every file is valid against [`16-article-schema.json`](16-article-schema.json). `additionalProperties` is false at every level: **an unknown key is a failure, not a silent drop.** A file that does not validate goes back to the bot that wrote it.

## Fields and where they land

Reviewer copies the page fields from each locale's file into the page unchanged. Nothing is rewritten on the way.

| Article field | Page field | Limit | Rule |
|---|---|---|---|
| `title` | `locale._<locale>.title` | 1–60 characters | plain text; the page `h1` and meta title; [`09/E21`](09-editorial-guidelines.md#rules) |
| `description` | `locale._<locale>.description` | 150–300 characters | plain text; the perex, one to three sentences; also the page's meta description |
| `seo_title` | `locale._<locale>.seo.title` and `locale._<locale>.seo_title` | 1–60 characters | plain text; same value in both fields; [`09/E22`](09-editorial-guidelines.md#rules) |
| `seo_description` | `locale._<locale>.seo.description` and `locale._<locale>.seo_description` | 120–155 characters | plain text; same value in both fields; [`09/E22`](09-editorial-guidelines.md#rules) |
| `slug` | `sk`: `uid`; every locale: the path of `url._<COUNTRY>` and the address row `_id` | 3–90 characters | see [Slug](#slug) |
| `blocks` | `blocks._<locale>` | see [Body](#body) | copied as the array, block by block |
| `tags` | `tags` | — | `[]` while the tags collection is empty ([`11-storefront-data.md`](11-storefront-data.md#tags)) |
| `cover.file` | none; `locale._<locale>.image` stays `""` | — | see [Cover and AI label](#cover-and-ai-label) |

Every other field is the sidecar: it tells Reviewer what the article uses and never reaches the page.

**Counting characters.** Count Unicode characters, spaces included, as the reader sees them.

**Plain text** means no `<`, `>`, `&`, or `"`, and no line break. Write „…“ for quotation marks and „a“ for „&“.

### Slug

- Lowercase ASCII letters, digits, and single hyphens: `^[a-z0-9]+(-[a-z0-9]+)*$`. Transliterate diacritics (`č` → `c`, `ô` → `o`).
- **Never contains `-g`, `-p`, `-c`, `-n`, or `-a` followed by a digit**, because the router reads that as an id ([`11-storefront-data.md`](11-storefront-data.md#fields)).
- **Never contains `faq`**, because a `uid` with `faq` switches the page to the FAQ layout.
- The Slovak slug becomes the page `uid`, so it is unique in the whole `pages` collection. Every slug is unique on its host among the address rows.
- The address is `https://<host>/<blog segment>/<slug>`, composed by Reviewer from the current environment. **No article file carries a host.**

## Body

`blocks` is an ordered array of the five block types that render, with the fields in [`11-storefront-data.md`](11-storefront-data.md#body-blocks). Nothing else.

| Rule | Fails when |
|---|---|
| Types are `header`, `paragraph`, `list`, `image`, `HTML` | any other type |
| `header.level` is 2, 3, or 4 | a level-1 header, or level 5 or 6 |
| At least two level-2 headers | fewer than two |
| The body opens with two `paragraph` blocks | the first or second block is not a paragraph |
| The last block is the AI label paragraph | the label is missing or moved |
| `id` is `b` plus two digits (`b01`, `b02`, …), unique, in rising order | a duplicate or skipped pattern |
| Every translation has the same ids, types, and order as the Slovak article | a block added, dropped, or moved |
| A table or a widget is an `HTML` block | a table or widget in any other block |
| `HTML` blocks carry `code`, `style: ""`, `localization: {}` | a non-empty `style`, a missing or non-empty `localization` |
| Body words, the AI label excluded, are 600 or more in Slovak | a Slovak body under 600 words |

Do not set `align` on headers or paragraphs; the storefront's default alignment applies.

Use a `list` for steps and enumerations and a table where numbers or models are compared. A comparison written as a paragraph of numbers fails.

### Text fields

Header `text`, paragraph `text`, and list item `content` render as raw HTML on 21 storefronts. They carry this subset and nothing more:

| Allowed | Form |
|---|---|
| bold | `<b>…</b>` or `<strong>…</strong>` |
| italic | `<i>…</i>` or `<em>…</em>` |
| link | `<a href="/…">…</a>`, a site-relative path; see [Links](#links) |

- **Every other `<` is written `&lt;` and every other `&` is written `&amp;`.** A product name `Puzzle <Mini>` is written `Puzzle &lt;Mini&gt;` and renders as text.
- Allowed entities: `&amp;`, `&lt;`, `&gt;`, `&quot;`, `&apos;`, `&nbsp;`. A bare `&` fails, and so does any numeric entity (`&#…;`).
- **No attribute other than `href` on `a`.** No `span`, `div`, `br`, `img`, `class`, `style`, `id`, `target`, or `rel`.
- **No absolute link.** An `href` that does not start with a single `/` fails: `https://…`, `//…`, `mailto:`, `javascript:`.
- Headers carry no link.

Good:

```json
{ "id": "b07", "type": "paragraph", "data": { "text": "Na drobné diely stačí <strong>kvapka lepidla</strong> na špičke špáradla. Ako na to, ukazujeme v článku <a href=\"/blog/kedy-pri-drevenom-3d-puzzle-siahnut-po-lepidle\">o lepení</a>." } }
```

Bad (a `span`, an absolute link, a raw `&`):

```json
{ "id": "b07", "type": "paragraph", "data": { "text": "<span class=\"hint\">Lepidlo & štetec</span> nájdeš <a href=\"https://<host SK>/lepidlo-p123\">tu</a>." } }
```

### Tables

A table is an `HTML` block whose `code` is exactly this shape, with one header row and at least two body rows:

```html
<div style="overflow-x:auto"><table><thead><tr><th>Typ modelu</th><th>Počet dielov</th><th>Čas skladania</th></tr></thead><tbody><tr><td>Hudobná skrinka</td><td>150 – 250</td><td>3 – 5 h</td></tr><tr><td>Book nook</td><td>200 – 350</td><td>6 – 12 h</td></tr></tbody></table></div>
```

- Allowed tags: `div` (only the wrapper above, with that exact `style`), `table`, `thead`, `tbody`, `tr`, `th`, `td`, and inside cells the text subset above.
- No attribute on any table tag.
- Cell text is escaped like a widget slot ([`15-widgets.md`](15-widgets.md#escaping)): **no `{` or `}` anywhere in `code`**, so the pages service's `>{…}<` replacement never fires.
- Figures about a specific kit come from its catalog parameters ([`09/E17`](09-editorial-guidelines.md#rules)). A table naming kits is product content and counts under [`09/E4`](09-editorial-guidelines.md#rules) and [`09/E6`](09-editorial-guidelines.md#rules).

### Widgets

A widget is an `HTML` block whose `code` is one of the five templates in [`15-widgets.md`](15-widgets.md), filled. **No other HTML reaches an `HTML` block**: no hand-written markup, no copied markup from a source site.

### Images

- An `image` block carries **a product photo only**: `file.url` is an entry of `images` of a product in `products_used`, and it starts with the CDN origin from [`10-environments.md`](10-environments.md). This is the one place a full address is written, because the image block needs it. In examples it is `<CDN origin>/…`.
- `file.width` and `file.height` are the photo's pixel size.
- `caption` is plain text and is also the alt text. It says what the photo shows ("Hotový model hudobnej skrinky zboku"), not a sales line. A product photo is product content and counts under [`09/E4`](09-editorial-guidelines.md#rules).
- **No AI illustration in the body.** An image block needs a CDN address, and the pipeline has no upload path ([`11-storefront-data.md`](11-storefront-data.md#cover-image)). An image block whose `url` is not a catalog photo fails.

## Links

Every link is site-relative: a path that starts with `/`, never a host.

| Link | Path | Where it comes from |
|---|---|---|
| product | the path of `url._<COUNTRY>` for the article's country, ending in `-p<id>` | [`11-storefront-data.md`](11-storefront-data.md#products) |
| another article | the path of that page's `url._<COUNTRY>`, for an enabled blog page only | [`11-storefront-data.md`](11-storefront-data.md#internal-links-to-other-articles) |

- **Each locale uses its own country's paths.** The Czech file links the path of `url._CZ`, never the Slovak one.
- Category pages are not in the storefront data you read, so you do not link them.
- A link you did not resolve in this run is omitted, not guessed.
- Every link is recorded: products in `products_used`, articles in `internal_links`.
- Link counts per pillar are in [`09/E7`](09-editorial-guidelines.md#rules).

## Cover and AI label

The cover is an AI illustration ([`09/E23`](09-editorial-guidelines.md#rules)). Creator writes it to `runs/<run_id>/cover.png` (or `cover.jpg`), landscape, at least 1200 × 675 pixels, and records it in `cover`:

| Field | Value |
|---|---|
| `cover.file` | `cover.png` or `cover.jpg` |
| `cover.prompt` | the full prompt used, in English |
| `cover.ai_label` | the label below, in the file's language |

The cover is not uploaded by any bot, so it never shows in the body. **The label therefore lives in the body itself: the last block of every locale is this paragraph**, so the page says the cover is illustrative in all 21 languages once the Editor uploads it.

Slovak label text, exactly:

```text
Titulný obrázok je ilustračný, vytvorila ho umelá inteligencia.
```

Slovak last block, exactly:

```json
{ "id": "b24", "type": "paragraph", "data": { "text": "<em>Titulný obrázok je ilustračný, vytvorila ho umelá inteligencia.</em>" } }
```

- `cover.ai_label` equals the text inside `<em>…</em>` of the last block. Translator translates both, identically.
- The prompt asks for no product, logo, packaging, text, numbers, or price in the image.
- Reviewer's message to the Editor names the cover file and says it is an AI illustration to upload before enabling ([`18-review-and-write.md`](18-review-and-write.md)).
- **A cover without `ai_label`, a label that differs from the text above, or a body without the label paragraph fails.**

## Sidecar fields

These fields do not reach the page. Reviewer checks the article against them and against the storefront.

| Field | What it holds |
|---|---|
| `run_id` | the run id, `YYYY-MM-DD-mon` or `YYYY-MM-DD-wed`; never changes on revision |
| `round` | Slovak file: the Slovak round, 1 to 3. Translation file: the translation round, 1 to 3 |
| `locale` | the file's locale key (`sk`, `cs`, …) |
| `topic_key`, `pillar` | copied from the plan row in `runs/<run_id>/row.tsv` |
| `products_used` | one entry per product anywhere in the file: `product_id`, `name` as written, `path` for this locale's country, `used_in` (block ids), `checked_at` (when the availability rule passed) |
| `reviews_quoted` | one entry per quote widget: `review_id`, `product_id`, `language` of the review, `block_id`, `text` as stored |
| `internal_links` | one entry per link to another article: `page_id`, `path`, `block_id` |
| `html_blocks` | one entry per `HTML` block: `block_id` and `kind` (`TABLE`, `PRODUCT_CARD`, `PRODUCT_GRID`, `TIP`, `QUOTE`, `FAQ`) |
| `sources` | what informed the article: `kind` (`INSPIRED_BY`, `COMMUNITY`, `FACT`), `ref` (a source-list entry, a `community/<topic_key>/` path, or the address of the page that confirms a fact), `note` |
| `word_counts` | Slovak file only: `body` and `product` words as defined in [`09-editorial-guidelines.md`](09-editorial-guidelines.md#terms) |
| `cover` | `file`, `prompt`, `ai_label` |

- Every product in the body appears in `products_used`, and every entry there appears in the body. A product fails the availability rule in [`11-storefront-data.md`](11-storefront-data.md#sold-in-all-21-countries) at the time of `checked_at` → it is not in the article.
- Every `HTML` block appears exactly once in `html_blocks`, and every entry points at an `HTML` block.
- `sources` never carries copied text. A source site informs the topic; its words and structure do not enter the article.
- A translation keeps `products_used[].product_id`, `reviews_quoted`, `html_blocks`, and `internal_links[].page_id` equal to the Slovak file; only `name`, `path`, and the texts change. One exception: when a linked article has no address for that country, the translation drops the link, keeps its text, and omits that entry from `internal_links` ([`19-translator.md`](19-translator.md)).

## What fails

Reviewer returns the article with the failing rule named when any of these holds:

- a level-1 header, or a block type outside the five;
- a text field with a tag outside the subset, a `span`, an attribute other than `href`, an absolute link, or a bare `<` or `&`;
- an `HTML` block with a non-empty `style`, without `localization: {}`, with any `{` or `}`, with an unfilled `[[slot]]`, or with markup that is not a table or a filled template;
- a widget carrying a price, a discount, or a currency;
- a product grid with fewer than three or more than six products;
- a quote whose text differs from the stored review;
- a cover without the AI label, or a body without the label paragraph;
- a field over its limit, or a slug that breaks the slug rules;
- a file that does not validate against [`16-article-schema.json`](16-article-schema.json).
