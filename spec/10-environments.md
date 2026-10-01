# 10 — Environments

One file says which storefront a run talks to: which databases it reads, which database Reviewer writes, and which 21 hosts the addresses use. These things belong together. Mixing them is an error on which you stop the run.

**No other file carries a storefront host, a database name, or a secret name.** Other files name a field of this file, such as "the pages database" or "the host for `CZ`". When the move from development to production happens, one line in this file changes — the `current` mark.

## Current environment

**current:** `development`

## Pairs

| Field | `development` | `production` |
|---|---|---|
| Repository branch (bots sync and push) | `dry-run`: created from `main` before the dry run, never merged into `main` | `main` |
| Storefront site id (`robotoys-ui: config/config_eshops.json`) | `1000000003` | `1000000002` |
| Pages database (blog pages, authors, categories, tags) | `robotoys_pages_devel` | `robotoys_pages_live` |
| SEO database (address rows) | `robotoys_seo_devel` | `robotoys_seo_live` |
| Product database | `robotoys_ecommerce_devel` | `robotoys_ecommerce_live` |
| Reviews database | `robotoys_reviews_live` | `robotoys_reviews_live` |
| Blog category id (`categories._id`) | `7` | `7` |
| Author id (`authors.uid`) | `1` | `1` |
| Database credential name | `ROBOTOYS_MONGO` | `ROBOTOYS_MONGO` |
| Search Console property, Slovak | `sc-domain:robotoys.sk` | `sc-domain:robotoys.sk` |
| Search Console property, translations | `sc-domain:robotoys.eu` | `sc-domain:robotoys.eu` |

**The repository branch keeps the two environments' state apart.** Plan statuses, run directories, ledger rows, and Search Console snapshots that a run pushes land on the branch of the current column only, so a development run never marks a topic `USED`, `WRITTEN`, or `HELD` for production. Bots push only run artifacts, plan rows, ledger rows, and snapshots, and only to that branch. Changes to the specification itself land on `main` through the owner, never through a bot; the owner brings them into `dry-run` from `main`. The marker on the branch a bot syncs is the marker that applies, and the owner switches the bots to `main` in the same change that sets `current` to `production`.

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

### Shared services

The reviews API and the CDN origin are the same in both environments. The pages API is one service; it picks the shop from the request `Host`.

| Service | Origin | Who may write |
|---|---|---|
| Admin UI | `https://robotoys.sk/admin` and `https://robotoys.sk/pages/admin` | nobody; the Editor uses it to enable a page |
| Pages API, production | `https://robotoys.sk/pages/api` (`Host: robotoys.sk` selects the live shop) | Reviewer, one save per run, production only |
| Pages API, development | the same service on the server at `127.0.0.1:8085`, `Host: dev.robotoys.sk` | Reviewer, one save per run, development only |
| Reviews API | `https://robotoys.sk/reviews/api` | nobody; read only |
| CDN | `https://cdn.robotoys.sk` | Reviewer, one cover upload per run |

The public dev site does not route `/pages/api` to this service, and `https://robotoys.sk/pages/api` always selects the live shop. A development save therefore goes through the SSH host of the database tunnel, local forward `127.0.0.1:18085` to `127.0.0.1:8085`, with header `Host: dev.robotoys.sk`. **A development save sent to host `robotoys.sk` is a stop you do not make.** The CDN upload is always `POST https://cdn.robotoys.sk/`; the pages service downloads that temporary file whichever shop it is writing.

`PUT /pages/api/publish` is the admin button Publikovať. It requests `GET https://<first shop host>/restart` and restarts the storefront. No bot sends it.

Development also reads the production reviews and users databases. The Editor enables pages only in the shop of the current environment.

## Credentials

Only secret **names** belong here. Values live in the bot platform's secret store and never appear in this repository, in a bot instruction, or in the group chat.

| Secret name | Held by | What it allows |
|---|---|---|
| `ROBOTOYS_MONGO` | all four bots | the one database connection string, used for everything, through the tunnel on `127.0.0.1:27027` |
| `GSC_SERVICE_ACCOUNT_JSON` | Planner | Search Console read (`webmasters.readonly`) on both properties |
| `GOOGLE_ADS_OAUTH_CLIENT_SECRET` | Planner | OAuth client secret of the Google Ads OAuth client (the client id is public and listed below) |
| `GOOGLE_ADS_REFRESH_TOKEN` | Planner | OAuth refresh token for the Ads user, scope `adwords` |
| `GH_TOKEN` | all four bots | fine-grained token, contents read and write on this repository only |

**One credential, `ROBOTOYS_MONGO`, serves every bot and every database.** It does not separate reading from writing, and the database does not enforce it, so the rules do. Only Reviewer writes, and only by the one pages API save of the current environment ([`11-storefront-data.md`](11-storefront-data.md#posting-the-page)). No bot runs `update`, `delete`, or `drop` itself, and no bot touches a database of the other column. A write refused for any reason is a stop.

### Google Ads account

| Field | Value |
|---|---|
| Customer ID (10 digits, no dashes) | `9049589988` (Robotoys, EUR) |
| Manager login customer ID | `8552457100` (manager account, linked to the customer above) |
| API version | `v23` |
| Google Cloud project | `robotoys-blog`, Google Ads API access level Basic |
| OAuth client ID | `1028602336391-m0msfcmohkqss7lop8fvgoso7a1lls2l.apps.googleusercontent.com` (public value) |

No developer token is used: Google no longer issues one, and the request works without the `developer-token` header.
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

A development page post needs a second forward on the same SSH host: local `127.0.0.1:18085` to remote `127.0.0.1:8085`. Check it the same way. When it is down, start `ssh -N -f -o BatchMode=yes -o ExitOnForwardFailure=yes -L 127.0.0.1:18085:127.0.0.1:8085 robotoys-mongo`. The request then uses `Host: dev.robotoys.sk`, as in [Shared services](#shared-services). Production does not use this forward.

## Git commits and pushes

Bots commit and push to this repository as the repository owner's GitHub login, with `GH_TOKEN`. No bot identity is invented.

1. Authenticate git with `GH_TOKEN` (`gh auth setup-git`, or HTTPS). Do not print the token.
2. Set author and committer to the owner's login and noreply address on every commit.
3. Push with the same login, to the repository branch of the current environment only. The token is limited to this repository, so a push anywhere else fails by design.

Scheduled runs start without anyone watching, so `git` against this repository must be approved in advance on the bot computer. When it is not, the run stops at the commit and says `git commit/push blocked — approval required`, in the failure shape of [`07-report-format.md`](07-report-format.md). A bot never pastes a file into the chat instead of pushing it; the next bot's input is the pushed file.

## What you may and may not do

- You read the current environment's databases. If you are Reviewer, you post one new disabled page through that environment's pages API, which stores the page, the cover, and the address rows ([`11-storefront-data.md`](11-storefront-data.md#posting-the-page)).
- Reading one environment's products and posting to the other environment's pages API is an error on which you stop the run and name what you mixed.
- You compose every address from the current environment's hosts. A development page carrying a production host is a failed write.
- You never write to an enabled page, a non-blog page, or any product, review, user, or order.
- You never use the admin UI, the reviews API, or `PUT /pages/api/publish`. The only CDN write is the cover upload inside the page post.

## What must be ready before the first run

This is not a bot's job. Without it the dry run does not start.

- four bot identities: Planner, Creator, Reviewer, Translator
- the SSH host `robotoys-mongo` on the bot computer and the tunnel check passing
- `ROBOTOYS_MONGO` on all four bots
- the Search Console service account added as a restricted user to both properties
- `GH_TOKEN` on all four bots, limited to this repository
- the schedules: Planner weekly Monday 06:00 and monthly at 06:00 on the Monday of the last full week (in place of that Monday's weekly check), Creator Monday and Wednesday 09:00 (Europe/Bratislava)
- the group chat in which one bot's message starts the next
- optional: Google Ads Basic access on the Cloud project, the account values above, and the two Ads secrets

## How you verify the pair fits

Before the first production write, the dry run shows a page written in the development pages database and none in the production one. `ROBOTOYS_MONGO` does not limit the database itself, so this is checked by reading the two pages databases and the address rows, not assumed from a hostname.
