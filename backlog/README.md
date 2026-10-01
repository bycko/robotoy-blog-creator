# Editorial plan

The plan of Slovak articles, two a week: one on Monday, one on Wednesday. Planner keeps it at least four weeks ahead; Creator takes its topic from it and from nothing else.

The rules are in [`../spec/13-planner.md`](../spec/13-planner.md). Topic keys and deduplication are in [`../spec/08-ledger.md`](../spec/08-ledger.md); pillars are in [`../spec/03-pillars.md`](../spec/03-pillars.md).

## File

[`editorial-plan.tsv`](editorial-plan.tsv), tab-separated, UTF-8, with this header:

```
week	publish_on	topic_key	pillar	working_title	reader	reader_question	must_answer	holiday_key	product_hint	tags	reason	inspired_by	status	origin
```

| Column | Short meaning |
|---|---|
| `week` | ISO week of `publish_on`, `2026-W41` |
| `publish_on` | `YYYY-MM-DD`, a Monday, a Wednesday or a Friday; the writing day and the day the article goes public |
| `topic_key` | `<pillar>-<subject>`, `GIFT` keys end with the year |
| `pillar` | `GUIDE`, `INSPIRATION`, `GIFT`, `COMMUNITY` |
| `working_title` | Slovak, at most 60 characters |
| `reader` | one concrete reader, Slovak |
| `reader_question` | the reader's own question, Slovak |
| `must_answer` | 2–5 items separated by ` ; ` |
| `holiday_key` | a key from [`../calendar/international-days.tsv`](../calendar/international-days.tsv), or `-` |
| `product_hint` | the kind of product that could help, never a product id or name, or `-` |
| `tags` | tag `uid`s, or `-` |
| `reason` | one Slovak sentence: why this topic, or why it was dropped |
| `inspired_by` | source keys from [`../sources/README.md`](../sources/README.md), or `-` |
| `status` | `PLANNED`, `USED`, `HELD`, `DROPPED` |
| `origin` | `PLANNER` or `HUMAN` |

Every row has exactly 15 fields; an empty value is `-`. Each Monday and each Wednesday holds at most one row that is not `DROPPED`.

## Who changes what

| Who | May |
|---|---|
| Planner | add `PLANNER` rows; re-date, reword, or drop its own `PLANNED` rows |
| Creator | set a `PLANNED` row to `USED` when it takes it |
| the bot that stops a run | set its `USED` row to `HELD` |
| the Editor | add or edit `HUMAN` rows; drop any `PLANNED` row |

**Planner never changes a `USED`, `HELD`, or `HUMAN` row, and never writes a `COMMUNITY` row.** Nobody deletes a row.

## Adding a row as the Editor

1. Pick a free Monday or Wednesday. Set `origin` to `HUMAN` and `status` to `PLANNED`.
2. Build the `topic_key` per [`../spec/08-ledger.md`](../spec/08-ledger.md#topic-key) and check the ledger: a covered topic does not come back.
3. Fill `reader`, `reader_question`, and `must_answer`. Without them the row is not ready and Creator skips it; Planner proposes values in its message.
4. For a `COMMUNITY` row, put the material in `community/<topic_key>/` before the writing day ([`../community/README.md`](../community/README.md)).
5. A `GIFT` row belongs inside a holiday window and never in a week with another holiday row ([`../spec/13-planner.md`](../spec/13-planner.md#holiday-limits)). Planner does not move your row when it breaks this; it tells you.

## Check

```bash
awk -F'\t' 'NF!=15 {print FILENAME": "NR": fields="NF}' backlog/editorial-plan.tsv
awk -F'\t' 'NR>1 && ($4 !~ /^(GUIDE|INSPIRATION|GIFT|COMMUNITY)$/ || $14 !~ /^(PLANNED|USED|HELD|DROPPED)$/ || $15 !~ /^(PLANNER|HUMAN)$/) {print NR}' backlog/editorial-plan.tsv
```

Both pass when they print nothing.

## Seed

The first eight rows, 2026-10-05 to 2026-10-28, were written by hand on 2026-09-30, before Planner's first run, from the live blog pages and the ledger. No Search Console or Keyword Planner data stands behind them, and their product hints are kinds that Planner checks against the availability rule on its first run. The first weekly check extends the plan past four weeks, and the monthly run on 2026-10-19 builds November.
