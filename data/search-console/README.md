# Search Console snapshots

Planner's saved Search Console pulls. Search Console keeps 16 months; these files are the only longer history, so the year-over-year season can be read from them.

How each pull is requested, which property it reads, and what it is used for is in [`../../spec/12-google-data.md`](../../spec/12-google-data.md).

## Who writes

Planner alone, on its monthly run and its weekly check, committed together with the plan. No other bot and no human edits these files.

## File names

`<end date>-<pull>.tsv`, where the end date is the latest final date of the pull, `YYYY-MM-DD`.

| Pull | Example |
|---|---|
| `monthly-sk-pages` | `2026-09-27-monthly-sk-pages.tsv` |
| `monthly-sk-queries` | `2026-09-27-monthly-sk-queries.tsv` |
| `monthly-translations-pages` | `2026-09-27-monthly-translations-pages.tsv` |
| `weekly-sk-queries` | `2026-09-27-weekly-sk-queries.tsv` |
| `monthly-translations-allpages` | `2026-09-27-monthly-translations-allpages.tsv` |
| `monthly-translations-queries` | `2026-09-27-monthly-translations-queries.tsv` |
| `monthly-translations-countries` | `2026-09-27-monthly-translations-countries.tsv` |
| `weekly-translations-queries` | `2026-09-27-weekly-translations-queries.tsv` |
| `weekly-translations-countries` | `2026-09-27-weekly-translations-countries.tsv` |

A `monthly-translations-*` or `weekly-translations-*` file is missing when the translations property could not be read; that month's report was labelled Slovak-only and its plan was ranked on the Slovak property alone.

The translations pulls without `-pages` have no page filter, because the queries on product, brand, and category pages show demand too. `monthly-translations-pages` is only the translated blog pages (per-article reporting); `monthly-translations-allpages` is every page of the property (totals and the pages that carry the demand). The 2026-09-28 files were first pulled on 2026-10-01; before that, ranking used only the Slovak property.

## Columns

Tab-separated, with a header, one row per API row, in the order the API returned them (clicks, highest first). A pull with zero rows is a file with the header only.

`*-pages.tsv`:

| Column | Meaning |
|---|---|
| `page` | full address as Search Console reports it |
| `clicks` | clicks in the range |
| `impressions` | impressions in the range |
| `ctr` | clicks ÷ impressions, 0 to 1 |
| `position` | average position, 1 is the top |

`*-queries.tsv`: the column `query` first, then the five columns above.

`*-countries.tsv`: the columns `query` and `country` (Search Console's three-letter country code, lower case, e.g. `pol`, `ita`), then `clicks`, `impressions`, `ctr`, `position`.

The sum of a queries file is lower than the matching pages file. Google omits rare, anonymized queries; this is expected. Example, translations property, 1.–28. 9. 2026: the date total is 164,579 impressions and 2,518 clicks; `monthly-translations-countries.tsv` holds 5,366 distinct queries with 90,629 impressions; the page rows add up to more than the date total, because grouping by page counts an impression on every page shown for the search.

## Pull log

`pulls.tsv` in this directory holds one row per file written, with a header:

| Column | Meaning |
|---|---|
| `file` | the file name |
| `property` | `Slovak` or `translations`, the property field in [`10-environments.md`](../../spec/10-environments.md) |
| `start_date` | first day, `YYYY-MM-DD`, Pacific time |
| `end_date` | last day, the latest final date |
| `dimensions` | `page`, `query,page`, or `query,country` |
| `rows` | rows written |
| `requests` | API requests needed, one per 25,000 rows |
| `pulled_at` | when Planner pulled it, ISO 8601 with offset |

## Retention

**Keep every file.** A rerun with the same end date overwrites that one file and its `pulls.tsv` row. An older file is never edited or deleted.

This repository stays private: the queries are the storefront's search data.
