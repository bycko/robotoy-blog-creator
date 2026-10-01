# 00 — Start here

This is the entry point. If you are a bot and you just pulled this repository, read from here.

## Which bot you are

Four separate bots, one repository, and one person. Identify yourself and read only your list. Everyone reads [`01-mission-and-rules.md`](01-mission-and-rules.md) first, and it wins.

| Role | What it does | Starts on | List |
|---|---|---|---|
| Planner | keeps the Slovak editorial plan four weeks ahead: a monthly run and a weekly check | its schedule | [Planner](#planner) |
| Creator | writes the Slovak article from one plan row | its schedule, or Reviewer's return | [Creator](#creator) |
| Reviewer | checks the Slovak article, then the 20 translations, then writes one public page | Creator's or Translator's chat line | [Reviewer](#reviewer) |
| Translator | makes the other 20 languages from the approved Slovak article | Reviewer's chat line, or the Editor's retry | [Translator](#translator) |
| Editor | a person: supplies community material, adds `HUMAN` rows and sources | — | not a bot; see [`../README.md`](../README.md) |

You do not know which one you are? **Stop and say so. Do not guess.**

## Schedule

All times are Europe/Bratislava.

| Bot | When |
|---|---|
| Planner, weekly check | Monday 06:00, every Monday except the monthly run's Monday |
| Planner, monthly run | Monday 06:00 of the last full week (Monday to Sunday) of the month, in place of that Monday's weekly check |
| Creator | Monday, Wednesday, and Friday 09:00 (three articles a week) |
| Reviewer | no schedule; an `@Reviewer` line from Creator or Translator |
| Translator | no schedule; an `@Translator` line from Reviewer, or a retry the Editor orders |

## Before every run

1. **Sync to the latest commit** of the repository branch of the current environment (the `Repository branch` row in [`10-environments.md`](10-environments.md#pairs): `dry-run` in development, `main` in production). `git pull` on that branch, then read its hash from the repository itself. Do not trust a hash you remember.
2. If the hash differs from your previous run, or you do not know it, reread your list from this file.
3. What is on that branch is what applies. A file you read before the pull does not count.
4. Read the `current` marker in [`10-environments.md`](10-environments.md#current-environment). Every database, host, credential, and the branch you push to come from that column. **When the branch you synced is not the repository branch of the marker's column, stop and name both.**
5. Before any database read, bring the tunnel up per [`10-environments.md`](10-environments.md#database-tunnel). Never connect to a public database address.
6. Every `git commit` and `git push` uses the owner's GitHub login through `GH_TOKEN` per [`10-environments.md`](10-environments.md#git-commits-and-pushes), and pushes only run artifacts, plan rows, ledger rows, and snapshots to the repository branch of the current environment. You never change a spec file; the owner lands spec changes on `main`.

## Order of the chat lines

Each line names a file in `runs/<run_id>/`; the next bot opens that file, not the chat text. The run directory and its files are in [`../runs/README.md`](../runs/README.md); the shape of every line is in [`07-report-format.md`](07-report-format.md).

1. Creator: article ready, `article.json`. `@Reviewer`
2. Reviewer: Slovak review, `review-sk-<n>.md`. `RETURNED` → `@Creator`; `APPROVED` → `@Translator`.
3. Translator: `translations/`. `@Reviewer`
4. Reviewer: translation review, `review-translations-<n>.md`. `RETURNED` → `@Translator`; `APPROVED` → Reviewer writes.
5. Reviewer: written, with the page `_id`; the page is public (`enabled`). No mention: the pipeline ends here.

A stop is posted to the chat without any mention; the Editor reads it there. The one exception is Translator's stop on a product, which ends `@Reviewer` so Reviewer can hold the row ([`07-report-format.md`](07-report-format.md#translator-stop-on-a-product)). Planner's messages start no bot and carry no mention. Creator's schedule reads the plan Planner pushed.

## Planner

1. [`01-mission-and-rules.md`](01-mission-and-rules.md)
2. [`10-environments.md`](10-environments.md)
3. [`11-storefront-data.md`](11-storefront-data.md) — blog pages and the availability rule
4. [`12-google-data.md`](12-google-data.md) — Search Console and Keyword Planner
5. [`13-planner.md`](13-planner.md) — **your contract**
6. [`03-pillars.md`](03-pillars.md)
7. [`08-ledger.md`](08-ledger.md)
8. [`../calendar/README.md`](../calendar/README.md) and [`../calendar/international-days.tsv`](../calendar/international-days.tsv)
9. [`../sources/README.md`](../sources/README.md)
10. [`../community/README.md`](../community/README.md) — whether a `COMMUNITY` row has its material
11. [`09-editorial-guidelines.md`](09-editorial-guidelines.md) — title rules
12. [`07-report-format.md`](07-report-format.md) — your messages

You write [`../backlog/editorial-plan.tsv`](../backlog/editorial-plan.tsv), `EXISTING` rows in [`../ledger/topics.tsv`](../ledger/topics.tsv), and snapshots in [`../data/search-console/`](../data/search-console/README.md).

## Creator

1. [`01-mission-and-rules.md`](01-mission-and-rules.md)
2. [`10-environments.md`](10-environments.md)
3. [`11-storefront-data.md`](11-storefront-data.md) — what you may read, the availability rule
4. [`17-creator.md`](17-creator.md) — **your contract**
5. [`../backlog/README.md`](../backlog/README.md) — the plan row's columns
6. [`03-pillars.md`](03-pillars.md)
7. [`09-editorial-guidelines.md`](09-editorial-guidelines.md)
8. [`14-article-contract.md`](14-article-contract.md)
9. [`15-widgets.md`](15-widgets.md) and the templates in [`../templates/widgets/`](../templates/widgets/product-card.html)
10. [`16-article-schema.json`](16-article-schema.json)
11. [`08-ledger.md`](08-ledger.md) and [`../community/README.md`](../community/README.md)
12. [`../sources/README.md`](../sources/README.md) — the sites an `inspired_by` key names
13. [`../runs/README.md`](../runs/README.md) — the run directory
14. [`07-report-format.md`](07-report-format.md) — your chat line

You write `runs/<run_id>/row.tsv`, `article.json`, and the cover, and set your row `USED` in the plan.

## Reviewer

1. [`01-mission-and-rules.md`](01-mission-and-rules.md)
2. [`10-environments.md`](10-environments.md) — the current marker, the database credential, the hosts
3. [`11-storefront-data.md`](11-storefront-data.md) — the page, address rows, `_id` allocation, the availability rule
4. [`18-review-and-write.md`](18-review-and-write.md) — **your contract**
5. [`../backlog/README.md`](../backlog/README.md)
6. [`03-pillars.md`](03-pillars.md)
7. [`09-editorial-guidelines.md`](09-editorial-guidelines.md) — the rules you cite
8. [`14-article-contract.md`](14-article-contract.md)
9. [`15-widgets.md`](15-widgets.md) and the templates in [`../templates/widgets/`](../templates/widgets/product-card.html)
10. [`16-article-schema.json`](16-article-schema.json)
11. [`08-ledger.md`](08-ledger.md)
12. [`../runs/README.md`](../runs/README.md)
13. [`07-report-format.md`](07-report-format.md) — your chat lines

You write the review files and `written.json`, you post the page once through the pages API (that call stores the page, the cover, and the 21 address rows), and you write `WRITTEN` and `HELD` ledger rows and a `HELD` status on a stop. You never read Creator's or Translator's reasoning.

## Translator

1. [`01-mission-and-rules.md`](01-mission-and-rules.md)
2. [`10-environments.md`](10-environments.md)
3. [`11-storefront-data.md`](11-storefront-data.md) — the locale table, products, pages, address rows
4. [`19-translator.md`](19-translator.md) — **your contract**
5. [`14-article-contract.md`](14-article-contract.md)
6. [`15-widgets.md`](15-widgets.md) and the templates in [`../templates/widgets/`](../templates/widgets/product-card.html)
7. [`09-editorial-guidelines.md`](09-editorial-guidelines.md)
8. [`16-article-schema.json`](16-article-schema.json)
9. [`../runs/README.md`](../runs/README.md)
10. [`07-report-format.md`](07-report-format.md) — your chat line

You write `runs/<run_id>/translations/`, nothing else.

## When you are unsure

- Two files contradict each other → the precedence in [`01-mission-and-rules.md`](01-mission-and-rules.md#precedence) decides. When it does not, **stop the run and name both files.**
- The previous stage's file is missing or empty → stop and name the file. You do not invent an input.
- The chat line and the file disagree → the file wins; when the file's verdict is not what the line claims, stop and name the file.
- A storefront collection is empty or unreadable → stop and name the database and collection.
