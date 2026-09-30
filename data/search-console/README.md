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

A `monthly-translations-pages` file is missing when the translations property could not be read; that month's report was labelled Slovak-only.

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

The sum of a queries file is lower than the matching pages file. Google omits rare, anonymized queries; this is expected.

## Pull log

`pulls.tsv` in this directory holds one row per file written, with a header:

| Column | Meaning |
|---|---|
| `file` | the file name |
| `property` | `Slovak` or `translations`, the property field in [`10-environments.md`](../../spec/10-environments.md) |
| `start_date` | first day, `YYYY-MM-DD`, Pacific time |
| `end_date` | last day, the latest final date |
| `dimensions` | `page` or `query,page` |
| `rows` | rows written |
| `requests` | API requests needed, one per 25,000 rows |
| `pulled_at` | when Planner pulled it, ISO 8601 with offset |

## Retention

**Keep every file.** A rerun with the same end date overwrites that one file and its `pulls.tsv` row. An older file is never edited or deleted.

This repository stays private: the queries are the storefront's search data.
