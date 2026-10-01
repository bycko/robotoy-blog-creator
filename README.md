# Robotoys blog pipeline

This repository is the **source of truth for four Grok bots** that deliver three articles a week to the Robotoys blog, in all 21 storefront languages.

It holds no code. It holds a specification, the editorial plan, the holiday calendar, the source list, the ledger, and one directory per article run. The storefront holds the pages, products, and reviews. No person enables an article: Reviewer writes it public once both checks pass.

## For a bot

Start at **[`spec/00-start-here.md`](spec/00-start-here.md)**. Identify your role and read only that role's list.

## How an article is made

1. **Planner** keeps [`backlog/editorial-plan.tsv`](backlog/editorial-plan.tsv) four weeks ahead. Once a month it builds the next month from three content pillars, Search Console, Keyword Planner, the holiday calendar, and the source list; every other Monday it adjusts open rows to fresh data.
2. **Creator** takes the next ready row on Monday, Wednesday, and Friday, and writes the Slovak article with product and community widgets and a photorealistic cover photo into `runs/<run_id>/`.
3. **Reviewer** checks the Slovak article without the writer's reasoning. It returns a failing article to Creator at most twice; a third failure holds the row for you.
4. **Translator** writes the other 20 languages from the approved Slovak article.
5. **Reviewer** checks the translations, re-checks every product, and posts **one public page** (`enabled`) with its cover, 21 languages, and 21 addresses through the same pages API request the admin uses to save an article.
6. **It is live.** The page is public the moment Reviewer writes it; you no longer enable it, so Creator's and Reviewer's checks are the final gate.

Every handoff is a file in `runs/<run_id>/`; a bot's line in the group chat only names the file ([`runs/README.md`](runs/README.md)).

| Bot | Profile | Starts |
|---|---|---|
| 1 Planner | [`agents/bot1_planner.md`](agents/bot1_planner.md) | weekly Monday 06:00; monthly Monday 06:00 of the last full week of the month, in place of that week's check |
| 2 Creator | [`agents/bot2_creator.md`](agents/bot2_creator.md) | Monday, Wednesday, and Friday 09:00, and Reviewer's return |
| 3 Reviewer | [`agents/bot3_reviewer.md`](agents/bot3_reviewer.md) | `@Reviewer` from Creator or Translator |
| 4 Translator | [`agents/bot4_translator.md`](agents/bot4_translator.md) | `@Translator` from Reviewer, or a retry you order |

All times are Europe/Bratislava. A profile is the text pasted into the bot platform; the detail stays in `spec/`. A profile carries no secret value and no storefront address.

## The Editor's jobs

| Job | When | Where |
|---|---|---|
| Supply community material, with a consent line for every person | before the writing day of a `COMMUNITY` row | [`community/README.md`](community/README.md) |
| Add a planned topic by hand | any time; set `origin` `HUMAN` | [`backlog/README.md`](backlog/README.md) |
| Decide a `HELD` row | after a stop | the plan and [`ledger/README.md`](ledger/README.md) |
| Refuse a topic for good | any time; append a `DROPPED` ledger row | [`ledger/README.md`](ledger/README.md) |
| Add or remove a source site | when Planner proposes one, or on your own | [`sources/README.md`](sources/README.md) |
| Add a holiday | when Planner proposes one | [`calendar/README.md`](calendar/README.md) |
| Act on a reported instruction | when a bot names fetched content that tried to instruct it | the chat line names where |
| Order a retry: name the run id and the bot (Creator, Reviewer, or Translator) | after a stop whose line says `opakovanie behu <run id>` | the group chat |

In development, the page is posted to the development shop, cover included, and goes public there only.

## Switching environment

[`spec/10-environments.md`](spec/10-environments.md) pairs the databases, the 21 hosts, the credentials, and the author and category ids of each environment. **Moving from development to production is one edit: the `current` marker in that file**, made on `main`, plus pointing the bots at `main`. Nothing else changes. Do it only after the dry run shows the pair fits, as that file describes.

**Each environment has its own repository branch.** In development the bots sync and push `dry-run`, a branch created from `main` before the dry run and never merged into it; in production they sync and push `main`. So plan statuses, run directories, and ledger rows from development never reach `main`. Bots push only run artifacts, plan rows, ledger rows, and snapshots. You land spec changes on `main` yourself and bring them into `dry-run` from there.

## Before the first run

The provisioning list is in [`spec/10-environments.md`](spec/10-environments.md#what-must-be-ready-before-the-first-run): four bot identities, the database tunnel, the database credential `ROBOTOYS_MONGO`, the Search Console service account, `GH_TOKEN`, the schedules, and the group chat. Keyword Planner access is optional; its steps are in [`spec/12-google-data.md`](spec/12-google-data.md#provisioning).

## Directory map

| Path | What it holds |
|---|---|
| [`spec/`](spec/00-start-here.md) | the rules every bot follows; [`01-mission-and-rules.md`](spec/01-mission-and-rules.md) binds all four |
| [`agents/`](agents/bot1_planner.md) | the four profiles pasted into the bot platform |
| [`templates/widgets/`](templates/widgets/product-card.html) | product card, product grid, tip box, customer quote, FAQ |
| [`backlog/`](backlog/README.md) | the editorial plan |
| [`calendar/`](calendar/README.md) | international and commemorative days, as rules |
| [`sources/`](sources/README.md) | competitor and inspiration sites |
| [`community/`](community/README.md) | material you supply for `COMMUNITY` articles |
| [`ledger/`](ledger/README.md) | topics already covered, and your decisions |
| [`data/search-console/`](data/search-console/README.md) | Planner's Search Console snapshots |
| [`runs/`](runs/README.md) | one directory per article run |
| `docs/` | plans and research notes for people; bots do not read them |

## What I want to change

| I want to… | File |
|---|---|
| understand what a bot must never do | [`spec/01-mission-and-rules.md`](spec/01-mission-and-rules.md) |
| change the pillars or the mix | [`spec/03-pillars.md`](spec/03-pillars.md) |
| change how articles read | [`spec/09-editorial-guidelines.md`](spec/09-editorial-guidelines.md) |
| change the planner | [`spec/13-planner.md`](spec/13-planner.md) |
| change the article fields or blocks | [`spec/14-article-contract.md`](spec/14-article-contract.md) |
| change a widget | [`spec/15-widgets.md`](spec/15-widgets.md) |
| change the writer | [`spec/17-creator.md`](spec/17-creator.md) |
| change review and the write | [`spec/18-review-and-write.md`](spec/18-review-and-write.md) |
| change translation | [`spec/19-translator.md`](spec/19-translator.md) |
| change a chat message | [`spec/07-report-format.md`](spec/07-report-format.md) |

This repository must stay private: it holds Search Console data and community material that names customers.
