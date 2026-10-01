# Runs

One directory per article run. Every handoff between the bots is a file in it: the plan row, the Slovak article, Reviewer's findings, the translations, the translation findings, and the write record. A retry lands on the same files.

## Run id

```
runs/YYYY-MM-DD-mon/
runs/YYYY-MM-DD-wed/
```

The directory name is the run id: Creator's schedule date in Europe/Bratislava, then `mon` or `wed`. It is also the `run_id` inside every article file and the `pipeline_run_id` on the page, which is how Reviewer's write recognises its own page on a replay.

- **The run id never changes on a retry or a revision.** A retry on a later day keeps the id of the run it retries.
- A retry writes into the existing directory. **There is never a second directory for one run.**
- Creator creates the directory only when it takes a row. A run that finds no ready row creates none ([`../spec/17-creator.md`](../spec/17-creator.md)).

## Layout

```
runs/<run_id>/
  row.tsv
  article.json
  cover.png                      or cover.jpg
  review-sk-<n>.md               n = 1, 2, 3
  translations/<locale>.json     20 files
  review-translations-<n>.md     n = 1, 2, 3
  written.json
```

| File | Written by | What it holds |
|---|---|---|
| `row.tsv` | Creator, in the same commit that sets the plan row `USED` | the plan header line and the taken row, exactly as in [`../backlog/editorial-plan.tsv`](../backlog/editorial-plan.tsv): 15 columns, `status` `USED` |
| `article.json` | Creator | the Slovak article of the current round, valid against [`../spec/16-article-schema.json`](../spec/16-article-schema.json). Overwritten on each revision; its `round` field says which |
| `cover.png` or `cover.jpg` | Creator | the cover photograph per [`../spec/14-article-contract.md`](../spec/14-article-contract.md#cover); shared by all 21 languages and posted with the page |
| `review-sk-<n>.md` | Reviewer | the Slovak findings of round `n`. Ends with one verdict line |
| `translations/<locale>.json` | Translator | one file per locale except `sk`, 20 in all; same schema as `article.json`, with `locale` set |
| `review-translations-<n>.md` | Reviewer | the translation findings of round `n`, one section per language. Ends with one verdict line |
| `written.json` | Reviewer | the write record, see below |

**Nothing else is written into a run directory. No bot deletes a run directory or a file in it.** A bot writes only its own files; earlier rounds of `article.json` and the translations live in git history.

### Verdict line

Every review file ends with exactly one of:

```
Verdict: APPROVED
Verdict: RETURNED
Verdict: STOPPED
```

| Round | `RETURNED` means | `STOPPED` means |
|---|---|---|
| `review-sk-1.md`, `review-sk-2.md` | Creator writes round 2 or 3 of `article.json` | — |
| `review-sk-3.md` | — | the third Slovak failure; the plan row is `HELD` |
| `review-translations-1.md`, `-2.md` | Translator rewrites only the failing languages | — |
| `review-translations-3.md` | — | the third translation failure; the plan row is `HELD` |

The newest review file of a kind is the one with the highest `n`. `n` of a Slovak review equals the `round` of the `article.json` it judged.

### `written.json`

Reviewer writes it after the page post, or when a replay finds the page already stored. A replay does not post again.

| Field | Value |
|---|---|
| `run_id` | the run id |
| `environment` | `development` or `production`, from [`../spec/10-environments.md`](../spec/10-environments.md) |
| `_id` | the page `_id` returned by the pages API |
| `sequence` | the page `sequence` stored by the service |
| `page_posted` | when the save returned, or when the page was found |
| `cover_url` | the Slovak `locale._sk.image` after the service filed it |
| `seo_rows` | the 21 stored addresses |
| `products_rechecked_at` | when the availability rule passed immediately before the write |
| `outcome` | `written`, `already-complete`, or `stopped` |

## Status of the plan row

Column 14 of [`../backlog/editorial-plan.tsv`](../backlog/editorial-plan.tsv):

```
PLANNED ──Creator takes it, pushed before writing──> USED ──page written──> USED
                                                      └──third failure, Slovak or translation, or a product withdrawn after approval──> HELD
PLANNED ──Planner or the Editor──> DROPPED, with a reason
```

The ledger ([`../ledger/topics.tsv`](../ledger/topics.tsv)) gets a `WRITTEN` row from Reviewer after a successful write, and a `HELD` row when a run stops.

## Order of the chat lines

Each line names a file in this directory; the next bot opens that file, not the chat text ([`../spec/07-report-format.md`](../spec/07-report-format.md)).

1. Creator: article ready, `article.json`.
2. Reviewer: Slovak review, `review-sk-<n>.md`.
3. On `RETURNED`, Creator revises and posts again; on `APPROVED`, Translator translates.
4. Translator: `translations/`.
5. Reviewer: translation review, `review-translations-<n>.md`.
6. On `RETURNED`, Translator fixes the failing languages; on `APPROVED`, Reviewer writes.
7. Reviewer: written, with the page `_id` and „enable by <publish_on>“.

## Example

```
runs/2026-10-05-mon/
  row.tsv
  article.json                   "round": 2
  cover.png
  review-sk-1.md                 Verdict: RETURNED
  review-sk-2.md                 Verdict: APPROVED
  translations/bg.json … translations/sv.json
  review-translations-1.md       Verdict: APPROVED
  written.json
```
