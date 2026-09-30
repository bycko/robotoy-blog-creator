# Holiday calendar

International and commemorative days on which a Robotoys article can be timed, with their Slovak dates. Planner reads it on every run; how it places a holiday row is in [`../spec/13-planner.md`](../spec/13-planner.md#holidays).

**No state holidays.** National and civic anniversaries, and public days off as such, are out of identity ([`../spec/03-pillars.md`](../spec/03-pillars.md#out-of-identity)). Neither are sale events.

The file stores **rules, not dates**, so it never needs a yearly edit.

## File

[`international-days.tsv`](international-days.tsv), tab-separated, UTF-8, with this header:

```
holiday_key	name_sk	rule	kind	lead_weeks	pillar	angle	priority	verified	source
```

| # | Column | Meaning |
|---|---|---|
| 1 | `holiday_key` | lowercase English, hyphens, ASCII; the plan row's `holiday_key` and the occasion part of a `GIFT` topic key |
| 2 | `name_sk` | the Slovak name as an article would write it |
| 3 | `rule` | how to compute the date in a given year, see [Rule grammar](#rule-grammar) |
| 4 | `kind` | `INTERNATIONAL`, `COMMEMORATIVE`, or `COMMERCIAL`, see [Kinds](#kinds) |
| 5 | `lead_weeks` | `3` or `4`: how many weeks before the day the article is published |
| 6 | `pillar` | the pillar of the day's row: `GIFT` or `INSPIRATION` |
| 7 | `angle` | what the article is about for this day, in English, one line |
| 8 | `priority` | `1`, `2`, or `3`; when two days want the same week, the lower number keeps it |
| 9 | `verified` | `yes` when the date rule was checked in the cited source; only `yes` rows exist here |
| 10 | `source` | the address where the date rule was checked |

Every row has exactly ten fields. No field is empty and none contains a TAB.

## Rule grammar

One rule per row, fields separated by single spaces. `MM` is a two-digit month, `DD` a two-digit day, `DOW` one of `MON`, `TUE`, `WED`, `THU`, `FRI`, `SAT`, `SUN`.

| Rule | Meaning | Example |
|---|---|---|
| `fixed MM-DD` | the same date every year | `fixed 06-01` is 1 June |
| `nth-weekday MM DOW N` | the `N`-th `DOW` of month `MM`, `N` from 1 to 4 | `nth-weekday 05 SUN 2` is the second Sunday of May |
| `last-weekday MM DOW` | the last `DOW` of month `MM` | `last-weekday 11 FRI` is the last Friday of November |

Compute a rule for every year the planning horizon touches. A horizon from December into February needs two years.

Worked out for 2027:

| Rule | Date |
|---|---|
| `nth-weekday 05 SUN 2` | 2027-05-09 |
| `nth-weekday 06 SUN 3` | 2027-06-20 |
| `nth-weekday 07 SUN 4` | 2027-07-25 |
| `fixed 06-01` | 2027-06-01, a Tuesday |

A rule Planner cannot parse stops the run with the file and the line named.

## Kinds

| Kind | What it is | Examples here |
|---|---|---|
| `INTERNATIONAL` | a day proclaimed or kept internationally, such as a UN or UNESCO day | Deň detí, Svetový deň kníh, Deň Zeme |
| `COMMEMORATIVE` | a day kept by custom in Slovakia that is not a state holiday | Deň matiek, Deň otcov, Deň učiteľov, Valentín, Mikuláš |
| `COMMERCIAL` | a season or day that exists in shopping and popular culture, used only for a gift or inspiration angle | the Christmas gift season, Halloween |

A `COMMERCIAL` day never becomes a sale, discount, or price article. The Christmas gift season is the run-up to 24 December as a time of choosing gifts; articles take no religious or state angle.

## Lead and window

`lead_weeks` is 3 for every day except the Christmas gift season, which is 4 because gifts are chosen earlier. The article lands three to four weeks before the day, not at its peak. A holiday row may sit only inside its window, from `lead_weeks + 1` weeks before the day to two weeks before it. The exact placement is in [`../spec/13-planner.md`](../spec/13-planner.md#placing-a-holiday-row).

## Left out

These were considered and are not in the file. Do not add them without a reason that answers the note.

| Day | Why not |
|---|---|
| Black Friday, Cyber Monday, Singles' Day (11 November) | sale events; out of identity |
| state holidays and days off (1 January, 29 August, 1 September, and others) | out of identity |
| World Teachers' Day (5 October) | Slovakia thanks teachers on 28 March; two teacher days would plan the same gift twice |
| Mesiac úcty k starším (October) | a month, not a day; the Day of Older Persons on 1 October carries it |
| National Hobby Month, National Miniature Month, NAME Day | United States observances, not international; no Slovak reader looks for them |
| National Jigsaw Puzzle Day (13 July) | the date could not be verified |

## Adding a day

Only the Editor adds a day. Planner may propose one in its monthly message.

1. Check the Slovak date rule in a source you can cite, and confirm the day is neither a state holiday nor a sale event.
2. Add one row with all ten fields and `verified` = `yes`. A day that cannot be verified is not added.
3. Choose `pillar`, `angle`, and `priority` so the day fits the pillars in [`../spec/03-pillars.md`](../spec/03-pillars.md).
4. Check the file:

```bash
awk -F'\t' 'NF!=10 {print NR": fields="NF}' calendar/international-days.tsv
awk -F'\t' 'NR>1 && $4 !~ /^(INTERNATIONAL|COMMEMORATIVE|COMMERCIAL)$/ {print NR}' calendar/international-days.tsv
```

Both pass when they print nothing.
