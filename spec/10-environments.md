# 10 — Environments

One file says which storefront a run talks to: which databases it reads, which database Reviewer writes, and which 21 hosts the addresses use. These things belong together. Mixing them is an error on which you stop the run.

**No other file carries a storefront host, a database name, or a secret name.** Other files name a field of this file, such as "the pages database" or "the host for `CZ`". When the move from development to production happens, one line in this file changes — the `current` mark.

## Current environment

**current:** `development`

## Pairs

| Field | `development` | `production` |
|---|---|---|
| Storefront site id (`robotoys-ui: config/config_eshops.json`) | `1000000003` | `1000000002` |
| Pages database (blog pages, authors, categories, tags) | `robotoys_pages_devel` | `robotoys_pages_live` |
| SEO database (address rows) | `robotoys_seo_devel` | `robotoys_seo_live` |
| Product database | `robotoys_ecommerce_devel` | `robotoys_ecommerce_live` |
| Reviews database | `robotoys_reviews_live` | `robotoys_reviews_live` |
| Blog category id (`categories._id`) | `7` | `7` |
| Author id (`authors.uid`) | `1` | `1` |
| Read credential name | `ROBOTOYS_MONGO_READ` | `ROBOTOYS_MONGO_READ` |
| Write credential name (Reviewer only) | `ROBOTOYS_MONGO_WRITE_DEVEL` | `ROBOTOYS_MONGO_WRITE_LIVE` |
| Search Console property, Slovak | `sc-domain:robotoys.sk` | `sc-domain:robotoys.sk` |
| Search Console property, translations | `sc-domain:robotoys.eu` | `sc-domain:robotoys.eu` |

Search Console always reads the production properties, because development has no search traffic. Ranking uses the Slovak property only; reporting adds the translations property ([`12-google-data.md`](12-google-data.md)).

The author id and category id were read from the production pages database on 2026-09-30: one author, uid `1`, name "Robotoys"; the blog category `_id` `7`, path `blog`. If the Editor creates a dedicated pipeline author, only this table changes.

### Hosts

Every address a bot composes is `https://<host>/<blog segment>/<slug>`, with the host from this table and the blog segment from [`11-storefront-data.md`](11-storefront-data.md). The order is the storefront order in `robotoys-ui: config/config_eshops.json`.

| Locale | Country key | `development` host | `production` host |
|---|---|---|---|
| `sk` | `SK` | `dev.robotoys.sk` | `robotoys.sk` |
| `bg` | `BG` | `dev.bg.robotoys.eu` | `bg.robotoys.eu` |
| `da` | `DK` | `dev.dk.robotoys.eu` | `dk.robotoys.eu` |
| `et` | `EE` | `dev.ee.robotoys.eu` | `ee.robotoys.eu` |
| `fr` | `FR` | `dev.fr.robotoys.eu` | `fr.robotoys.eu` |
| `el` | `GR` | `dev.gr.robotoys.eu` | `gr.robotoys.eu` |
| `nl` | `NL` | `dev.nl.robotoys.eu` | `nl.robotoys.eu` |
| `hr` | `HR` | `dev.hr.robotoys.eu` | `hr.robotoys.eu` |
| `lt` | `LT` | `dev.lt.robotoys.eu` | `lt.robotoys.eu` |
| `lv` | `LV` | `dev.lv.robotoys.eu` | `lv.robotoys.eu` |
| `pt` | `PT` | `dev.pt.robotoys.eu` | `pt.robotoys.eu` |
| `ro` | `RO` | `dev.ro.robotoys.eu` | `ro.robotoys.eu` |
| `sl` | `SI` | `dev.si.robotoys.eu` | `si.robotoys.eu` |
| `it` | `IT` | `dev.it.robotoys.eu` | `it.robotoys.eu` |
| `cs` | `CZ` | `dev.cz.robotoys.eu` | `cz.robotoys.eu` |
| `hu` | `HU` | `dev.hu.robotoys.eu` | `hu.robotoys.eu` |
| `de` | `DE` | `dev.de.robotoys.eu` | `de.robotoys.eu` |
| `pl` | `PL` | `dev.pl.robotoys.eu` | `pl.robotoys.eu` |
| `es` | `ES` | `dev.es.robotoys.eu` | `es.robotoys.eu` |
| `sv` | `SE` | `dev.se.robotoys.eu` | `se.robotoys.eu` |
| `en` | `EU` | `dev.robotoys.eu` | `robotoys.eu` |

### Shared production services

These services are the same in both environments. **Bots only read from them; no bot writes to, uploads to, or saves through any of them.**

| Service | Origin |
|---|---|
| Admin | `https://robotoys.sk/admin` |
| Pages API | `https://robotoys.sk/pages/api` |
| Reviews API | `https://robotoys.sk/reviews/api` |
| CDN | `https://cdn.robotoys.sk` |

Development also reads the production reviews and users databases. An Editor action in the admin during a development run touches production data, so a development run never asks the Editor for an admin save.

## Credentials

Only secret **names** belong here. Values live in the bot platform's secret store and never appear in this repository, in a bot instruction, or in the group chat.

| Secret name | Held by | What it allows |
|---|---|---|
| `ROBOTOYS_MONGO_READ` | all four bots | read on the pages, SEO, product, and reviews databases of both environments |
| `ROBOTOYS_MONGO_WRITE_DEVEL` | Reviewer | `find` and `insert` on `robotoys_pages_devel.pages` and `robotoys_seo_devel.seo`, nothing else |
| `ROBOTOYS_MONGO_WRITE_LIVE` | Reviewer | `find` and `insert` on `robotoys_pages_live.pages` and `robotoys_seo_live.seo`, nothing else |
| `GSC_SERVICE_ACCOUNT_JSON` | Planner | Search Console read (`webmasters.readonly`) on both properties |
| `GOOGLE_ADS_DEVELOPER_TOKEN` | Planner | Keyword Planner requests (optional until provisioned) |
| `GOOGLE_ADS_OAUTH_CLIENT` | Planner | OAuth client id and secret for Google Ads |
| `GOOGLE_ADS_REFRESH_TOKEN` | Planner | OAuth refresh token for the Ads user |
| `GH_TOKEN` | all four bots | fine-grained token, contents read and write on this repository only |

**No write role grants `update`, `delete`, or `drop`.** Each write credential is a database user created in its own environment, so the development credential is refused by the production pages database and the other way round. A write refused for permissions is a stop, never a reason to try the other credential.

### Google Ads account

| Field | Value |
|---|---|
| Customer ID (10 digits, no dashes) | supplied by the owner; `unset` until then |
| Manager login customer ID | supplied by the owner; `unset` until then |
| API version | pinned when access is granted; `unset` until then |

`AW-11153827010` in the storefront config is a conversion tag id, not the customer ID. While any row above is `unset`, Keyword Planner counts as unavailable ([`12-google-data.md`](12-google-data.md)).

## Database tunnel

The databases are not reachable on the public network. Every database connection goes through an SSH local forward.

| Field | Value |
|---|---|
| SSH config host | `robotoys-mongo` |
| Local bind | `127.0.0.1:27027` |
| Remote bind | `127.0.0.1:27017` |

The SSH user, hostname, and identity file live in `~/.ssh/config` on the bot computer as Host `robotoys-mongo`, with key authentication. They do not belong in this repository.

1. **Check:** `nc -z 127.0.0.1 27027`. If it succeeds, connect. Do not start a second forward.
2. **Start** when the check fails: `ssh -N -f -o BatchMode=yes -o ExitOnForwardFailure=yes robotoys-mongo`, then repeat the check for up to ten seconds.
3. **Stop** the run and name what failed if `ssh` is missing, the host is not configured, the key needs a passphrase, or the port stays closed. Never connect to a public database address, and never print the key or the connection string.

A stored connection string that points anywhere other than `127.0.0.1:27027` is a stop.

## Git commits and pushes

Bots commit and push to this repository as the repository owner's GitHub login, with `GH_TOKEN`. No bot identity is invented.

1. Authenticate git with `GH_TOKEN` (`gh auth setup-git`, or HTTPS). Do not print the token.
2. Set author and committer to the owner's login and noreply address on every commit.
3. Push with the same login. The token is limited to this repository, so a push anywhere else fails by design.

Scheduled runs start without anyone watching, so `git` against this repository must be approved in advance on the bot computer. When it is not, the run stops at the commit and says `git commit/push blocked — approval required`, in the failure shape of [`07-report-format.md`](07-report-format.md). A bot never pastes a file into the chat instead of pushing it; the next bot's input is the pushed file.

## What you may and may not do

- You read the current environment's databases and write, if you are Reviewer, only to the current environment's pages and SEO databases.
- Reading one environment's products and writing to the other environment's pages is an error on which you stop the run and name what you mixed.
- You compose every address from the current environment's hosts. A development page carrying a production host is a failed write.
- You never write to an enabled page, a non-blog page, or any product, review, user, or order.
- You never write through the admin, the pages API, the reviews API, or the CDN.

## What must be ready before the first run

This is not a bot's job. Without it the dry run does not start.

- four bot identities: Planner, Creator, Reviewer, Translator
- the SSH host `robotoys-mongo` on the bot computer and the tunnel check passing
- `ROBOTOYS_MONGO_READ` on all four bots
- `ROBOTOYS_MONGO_WRITE_DEVEL` and `ROBOTOYS_MONGO_WRITE_LIVE` on Reviewer only, each created in its own environment with the find-and-insert role above
- the Search Console service account added as a restricted user to both properties
- `GH_TOKEN` on all four bots, limited to this repository
- the schedules: Planner weekly Monday 06:00 and monthly on the Monday of the last full week, Creator Monday and Wednesday 09:00 (Europe/Bratislava)
- the group chat in which one bot's message starts the next
- optional: Google Ads Basic access, the customer IDs above, and the three Ads secrets

## How you verify the pair fits

Before the first production write, the dry run shows a page written with the development credential in the development pages database, none in the production one, and the development credential refused on the production pages database. A hostname is not enough evidence.
