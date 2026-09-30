# Ledger

The list of topics the blog already covers, and of the Editor's decisions about topics. It stops the same topic from coming back as a second article.

The rules are in [`../spec/08-ledger.md`](../spec/08-ledger.md). Blog pages in the storefront are checked there too; the ledger alone is not enough.

## Format

[`topics.tsv`](topics.tsv), tab-separated, with a header:

```
topic_key	pillar	run_id	page_id	status	date	note
```

| Column | Meaning |
|---|---|
| `topic_key` | `<pillar>-<subject>`, lowercase English, hyphens; `GIFT` keys end with the year |
| `pillar` | `GUIDE`, `INSPIRATION`, `GIFT`, `COMMUNITY` |
| `run_id` | the run that wrote the page; `-` for a page made outside the pipeline |
| `page_id` | the page `_id`; `-` when no page exists |
| `status` | `EXISTING`, `WRITTEN`, `HELD`, `DROPPED` |
| `date` | `YYYY-MM-DD` |
| `note` | the Slovak title, for people; `-` when there is none |

Every row has exactly seven fields; an empty value is `-`. Check with:

```bash
awk -F'\t' 'NF!=7 {print NR": fields="NF}' ledger/topics.tsv
```

It passes when it prints nothing.

## Filling

- Reviewer appends `WRITTEN` after it writes a page. A replay adds nothing.
- The bot that stops a run appends `HELD`.
- Planner appends `EXISTING` for a blog page it finds without a row.
- **The Editor appends `DROPPED` to refuse a topic.** A dropped topic does not come back.

Rows are only appended; the last row for a key is its current status. Do not delete rows. The seed rows, pages 17 to 45, were read from the production pages database on 2026-09-30.
