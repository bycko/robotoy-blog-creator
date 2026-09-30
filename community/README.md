# Community material

Material for `COMMUNITY` articles: challenge results, the build of the month, what builders say. The Editor supplies it; no bot collects it. The full rules are in [`../spec/08-ledger.md`](../spec/08-ledger.md#community-material).

## How to add a community article

1. Add a `COMMUNITY` row to the editorial plan with its `topic_key`, for example `community-challenge-results-2026-11` ([`../backlog/README.md`](../backlog/README.md)).
2. Create the folder `community/<topic_key>/` with `material.md` and, if there are photos, `photos/`.
3. Push before the row's writing day. Without usable material that day, Creator skips the row, takes the next one, and says the community row is waiting.

## `material.md`

```
## Facts

Novembrová výzva: Mechanické hodiny. 38 zaslaných stavieb, víťazi vybraní hlasovaním 1. decembra.

## Quotes

> Najviac ma potrápili ozubené kolesá, ale keď sa hodiny prvýkrát rozbehli, stálo to za to.

consent: Martin z Trnavy | name, quote | e-mail | 2026-11-03

## Photos

- photos/martin-hodiny.jpg — hotové hodiny na poličke
  consent: Martin z Trnavy | name, photo | e-mail | 2026-11-03
```

## Consent line

```
consent: <display name> | <scope: name, quote, photo> | <channel: e-mail, chat, form, in person> | <YYYY-MM-DD>
```

- **Nobody is named, quoted, or shown without a consent line.** Their item is not used.
- Only what the scope lists is used. A quote without `name` appears as "jeden zo staviteľov".
- Write the display name exactly as it may appear. Bots never add to it.
- **No e-mail addresses, phone numbers, or street addresses** anywhere in this folder. This repository must stay private, because it names customers.
- No photo showing a child's face.
- Bots cannot upload photos. You upload a photo in the admin when Reviewer's message asks for it.
- When someone withdraws consent, delete their item and its line.
