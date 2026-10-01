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

Reviewer copies the page fields from each locale's file into the page unchanged, except the cover image block. That block has no `file` in the article. At the save, Reviewer sets `file.url` and `file.path` from the CDN upload and drops `role` ([`11-storefront-data.md`](11-storefront-data.md#posting-the-page)).

| Article field | Page field | Limit | Rule |
|---|---|---|---|
| `title` | `locale._<locale>.title` | 1–60 characters | plain text; the page `h1` and meta title; [`09/E21`](09-editorial-guidelines.md#rules) |
| `description` | `locale._<locale>.description` | 150–300 characters | plain text; the perex, one to three sentences; also the page's meta description |
| `seo_title` | `locale._<locale>.seo_title` | 1–60 characters | plain text; the admin save sends this flat key; [`09/E22`](09-editorial-guidelines.md#rules) |
| `seo_description` | `locale._<locale>.seo_description` | 120–155 characters | plain text; the admin save sends this flat key; [`09/E22`](09-editorial-guidelines.md#rules) |
| `slug` | none: the storefront service builds `sk` `uid`, the path of `url._<COUNTRY>` and the address row `_id` from each locale's `title` | 3–90 characters | a proposal in the sidecar, used for the collision check; see [Slug](#slug) |
| `blocks` | `blocks._<locale>` | see [Body](#body) | copied as the array, block by block, except the cover block's `file` |
| `tags` | `tags` | — | `[]` while the tags collection is empty ([`11-storefront-data.md`](11-storefront-data.md#tags)) |
| `cover.file` | `locale._<locale>.image`, and the first block, after the pages service files the upload | — | see [Cover](#cover) |

Every other field is the sidecar: it tells Reviewer what the article uses and never reaches the page. Reviewer copies page fields unchanged, except the cover image block: its `file` does not exist in the article file, and Reviewer fills it at the save ([`11-storefront-data.md`](11-storefront-data.md#posting-the-page)).

**Counting characters.** Count Unicode characters, spaces included, as the reader sees them.

**Plain text** means no `<`, `>`, `&`, or `"`, and no line break. Write „…“ for quotation marks and „a“ for „&“.

### Slug

**The address is built from the title, not from `slug`.** The storefront service makes the Slovak `uid` and, in every language, the last path segment of `url._<COUNTRY>` and of the address row from that language's `title` ([`11-storefront-data.md`](11-storefront-data.md#posting-the-page)). It does so with the `slugify` function of `robotoys-ui` (`liqd-string`). **That rule is the one written in [`19-translator.md`](19-translator.md#transliteration), confirmed by the Editor on 2026-10-01; its transliteration table is the single source and is not copied here.** The `slug` field is a proposal in the sidecar: Creator computes it **with exactly that rule from the Slovak `title`**, so the checks below run on the real address.

- A letter the table of [`19-translator.md`](19-translator.md#transliteration) does not list (today `đ`; `ü` and `ß` are listed only in its German row) follows that table once Translator adds it. Creator invents no behaviour for such a letter and words a Slovak title so that it does not depend on it.
- A title whose slug would exceed 90 characters is shortened by Creator. A Slovak title of at most 60 characters never reaches that.
- Lowercase ASCII letters, digits, and single hyphens: `^[a-z0-9]+(-[a-z0-9]+)*$`. Diacritics become the base letter (`č` → `c`, `ô` → `o`); punctuation such as `:` or `,` is dropped.
- **Never contains `-g`, `-p`, `-c`, `-n`, or `-a` followed by a digit**, because the router reads that as an id ([`11-storefront-data.md`](11-storefront-data.md#fields)).
- **Never contains `faq`**, because a `uid` with `faq` switches the page to the FAQ layout.
- The slug the service makes from the Slovak title becomes the page `uid`, so it is unique in the whole `pages` collection and among the `seo` address rows of the Slovak host.
- When a check fails, **change the `title`**, not only the `slug` field: the title is what the service reads.
- The address is `https://<host>/<blog segment>/<slug the service made>`. **No article file carries a host.**

## Body

`blocks` is an ordered array of the five block types that render, with the fields in [`11-storefront-data.md`](11-storefront-data.md#body-blocks). Nothing else.

| Rule | Fails when |
|---|---|
| Types are `header`, `paragraph`, `list`, `image`, `HTML` | any other type |
| `header.level` is 2, 3, or 4 | a level-1 header, or level 5 or 6 |
| At least two level-2 headers | fewer than two |
| The body opens with the cover image, two paragraphs, then the contents list | block 1 is not the cover image, block 2 or 3 is not a paragraph, or block 4 is not the contents widget |
| `id` is `b` plus two digits (`b01`, `b02`, …), unique, in rising order | a duplicate or skipped pattern |
| Every translation has the same ids, types, and order as the Slovak article | a block added, dropped, or moved |
| A table or a widget is an `HTML` block | a table or widget in any other block |
| `HTML` blocks carry `code`, `style: ""`, `localization: {}` | a non-empty `style`, a missing or non-empty `localization` |
| Body words are 600 or more in Slovak | a Slovak body under 600 words |

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

A widget is an `HTML` block whose `code` is one of the six templates in [`15-widgets.md`](15-widgets.md), filled. **No other HTML reaches an `HTML` block**: no hand-written markup, no copied markup from a source site.

### Headers and the contents list

Every level-2 header block carries an anchor in `tunes`, beside `data`, not inside it:

```json
{ "id": "b05", "type": "header", "data": { "text": "Čo je book nook a odkiaľ sa vzal", "level": 2 }, "tunes": { "anchorTune": { "anchor": "co-je-book-nook-a-odkial-sa-vzal" } } }
```

- `tunes.anchorTune.anchor` is the slug of that header's `text` **in that language**, made with the `slugify` rule of [`19-translator.md`](19-translator.md#transliteration). That file is the single source of the rule; it is not copied here. Creator makes the Slovak anchors; Translator makes the anchors of the 20 other languages. Each language therefore has its own ids.
- An anchor matches `^[a-z0-9]+(-[a-z0-9]+)*$`, is unique on the page, and does not equal another id on the page. A level-2 header whose text gives an empty slug fails; reword the header.
- The contents list item for that header uses the same string as `item_id` and links to `#<anchor>` ([`15-widgets.md`](15-widgets.md#contents)). Contents items and level-2 headers are in the same order. Reviewer checks that every `item_id` equals the slug of its header text in every language.
- Level 3 and 4 headers carry no `tunes`. The key `elementID` no longer exists in the article file.
- A level-2 header's `text` is plain text, the same words as that item in the contents list.
- **Not yet verified:** that the pages API keeps `tunes` when it creates the page (the page `PATCH`). If the service drops them, the anchors do not work; the page stays disabled and Reviewer reports it (Reviewer's rule in [`18-review-and-write.md`](18-review-and-write.md)). Runs written before this rule (for example `2026-10-07-wed`, `2026-10-05-mon`) use `elementID` or none; they are history and are not rewritten.

### Images

Two kinds of `image` block exist. The article file never carries `file.path` and never carries a `/tmp/` address. Reviewer adds those on the cover block only, at the save.

**Cover.** The first block, and no other:

```json
{ "id": "b01", "type": "image", "data": { "role": "cover", "caption": "Kedy pri drevenom 3D puzzle siahnuť po lepidle" } }
```

- `role` is `cover`. There is no `file`. The bytes live in `runs/<run_id>/cover.png` or `cover.jpg`, not in this block.
- `caption` equals that file's `title`. It is the alt text. It is plain text and not a sales line.
- Exactly one cover block. A second `role: "cover"` fails.

**Product photo.** Any later `image` block:

- `file.url` is an entry of `images` of a product in `products_used`, and it starts with the CDN origin from [`10-environments.md`](10-environments.md). This is the one place a full address is written in the article file. In examples it is `<CDN origin>/…`.
- `file.width` and `file.height` are the photo's pixel size. There is no `file.path` and no `role`.
- `caption` is plain text and is also the alt text. It says what the photo shows ("Hotový model hudobnej skrinky zboku"), not a sales line. A product photo is product content and counts under [`09/E4`](09-editorial-guidelines.md#rules).
- A product image whose `url` is not a catalog photo fails. A product image with `file.path` fails: the page save would upload it again ([`11-storefront-data.md`](11-storefront-data.md#posting-the-page)).

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

## Cover

The cover is a photorealistic photograph ([`09/E23`](09-editorial-guidelines.md#rules)) made for this article: its scene and main subject come from the article's title and topic, as in [`09-editorial-guidelines.md`](09-editorial-guidelines.md#cover). Creator writes it to `runs/<run_id>/cover.png` (or `cover.jpg`), landscape, at least 1200 × 675 pixels, and records it in `cover`:

| Field | Value |
|---|---|
| `cover.file` | `cover.png` or `cover.jpg` |
| `cover.prompt` | the full prompt used, in English |

Reviewer posts that file with the page ([`11-storefront-data.md`](11-storefront-data.md#posting-the-page)). The same upload becomes `locale._<locale>.image` (the `og:image`, which the page does not show) and the first block (the picture at the top of the article). The body does not say how the cover was made.

- The prompt describes a scene that fits the article's title and topic, in the photograph style of [`09-editorial-guidelines.md`](09-editorial-guidelines.md#cover): no readable text, logo, packaging, price, or recognizable shop kit.
- A missing cover file, or a `cover.file` that does not name it, fails.

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
| `html_blocks` | one entry per `HTML` block: `block_id` and `kind` (`TABLE`, `PRODUCT_CARD`, `PRODUCT_GRID`, `TIP`, `QUOTE`, `FAQ`, `TOC`) |
| `sources` | what informed the article: `kind` (`INSPIRED_BY`, `COMMUNITY`, `FACT`), `ref` (a source-list entry, a `community/<topic_key>/` path, or the address of the page that confirms a fact), `note` |
| `word_counts` | Slovak file only: `body` and `product` words as defined in [`09-editorial-guidelines.md`](09-editorial-guidelines.md#terms) |
| `cover` | `file`, `prompt` |

- Every product in the body appears in `products_used`, and every entry there appears in the body. A product fails the availability rule in [`11-storefront-data.md`](11-storefront-data.md#sold-in-all-21-countries) at the time of `checked_at` → it is not in the article.
- Every `HTML` block appears exactly once in `html_blocks`, and every entry points at an `HTML` block.
- `sources` never carries copied text. A source site informs the topic; its words and structure do not enter the article.
- A translation keeps `products_used[].product_id`, `reviews_quoted`, `html_blocks`, and `internal_links[].page_id` equal to the Slovak file; only `name`, `path`, and the texts change. One exception: when a linked article has no address for that country, the translation drops the link, keeps its text, and omits that entry from `internal_links` ([`19-translator.md`](19-translator.md)).

## What fails

Reviewer returns the article with the failing rule named when any of these holds:

- a level-1 header, a block type outside the five, a body that does not open with the cover, two paragraphs, and the contents list, a level-2 header without `tunes.anchorTune.anchor`, an anchor that is not the slug of its text, a duplicate anchor, or a contents list that does not match those headers;
- a text field with a tag outside the subset, a `span`, an attribute other than `href`, an absolute link, or a bare `<` or `&`;
- an `HTML` block with a non-empty `style`, without `localization: {}`, with any `{` or `}`, with an unfilled `[[slot]]`, or with markup that is not a table or a filled template;
- a widget carrying a price, a discount, or a currency;
- a product grid with fewer than three or more than six products;
- a quote whose text differs from the stored review;
- a missing cover file, or a `cover.file` that does not name it;
- a field over its limit, or a slug that breaks the slug rules;
- a file that does not validate against [`16-article-schema.json`](16-article-schema.json).
