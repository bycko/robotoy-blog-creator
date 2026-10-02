# Slovenská kontrola — 2026-10-02-fri, kolo 1

Reviewed      runs/2026-10-02-fri/article.json, round 1
Commit        1d145ece0a48019c91fdf148e7957b2682e3973a
Environment   production
Checked at    2026-10-02T09:08:32+02:00

## Findings

1. **09/E20** · block `b20` · Veta "vidieť ozubené kolesá a prevod na vlastné oči" tvrdí o produkte niečo, čo katalóg nepotvrdzuje. Popis produktu 198 uvádza kremenný mechanizmus, napájanie AAA/USB-C a LED osvetlenie, o ozubenom prevode nehovorí nič. Článok pritom vysvetľuje hodiny so závažiami a mechúrikmi, takže čitateľ by čakal rovnaký mechanizmus.
   Oprava: opísať len to, čo katalóg potvrdzuje (drevený model na poskladanie, 435 dielov, kremenný mechanizmus), a vynechať sľub ozubených kolies a prevodu.
2. **09/E20** · block `b06` · "aby ťahanie kukučky nebrzdilo meranie času" je dôvod, ktorý zdroje neuvádzajú. Zdroje hovoria len, že každý prevod (chod a úder) má vlastné závažie.
   Oprava: napísať, čo zdroje tvrdia (každý prevod má vlastné závažie, takže bežia nezávisle), bez vymysleného dôvodu.
3. **09/E20** · blocks `b03`, `b11`, `b12`, `b13` · Opis spustenia úderu nesedí so zdrojmi ani sám so sebou. V `b03` a `b13` úderový prevod mechúriky "stlačí", v `b12` ich vačka "zdvihne". V `b11` "páčka z nej spadne a uvoľní úderový prevod", pričom zdroj hovorí, že vačka páčku zdvihne a tá uvoľní úder.
   Oprava: použiť jedno sloveso podľa zdroja (vintageclockparts: vačka mechúriky zdvíha) vo všetkých štyroch blokoch a v `b11` opísať páčku tak, ako ju opisuje zdroj.
4. **09/E20** · block `b17` · "niekde je vôľa, prach alebo ohnutý čap" je zoznam príčin podaný ako fakt bez zdroja.
   Oprava: zmäkčiť ("môže to byť…") a ponechať len príčiny, ktoré zdroje spomínajú (prach na čapoch, viaznúci drôtik), alebo vetu vypustiť.
5. **09/E20** · blocks `b18`, `b23` · "hlavne vtedy, keď sa zmení tempo kyvadla" je nepodložené poradie príčin, zdroj uvádza dĺžku kyvadla, teplotu a nerovné postavenie bez poradia. "pri čistení a mazaní" je nepodložená rada, zdroj navyše hovorí, že mechúriky musia zostať suché a olej papier ničí. V odpovedi 3 vo FAQ "keď je kratšie alebo prevod viazne, môžu sa ponáhľať alebo zastaviť" pripisuje ponáhľanie viaznúcemu prevodu.
   Oprava: vypustiť "hlavne" a "a mazaní"; vo FAQ oddeliť: kratšie kyvadlo hodiny ponáhľa, viaznúci prevod ich spomaľuje alebo zastaví.
6. **09/E20** · block `b09` · "kyvadlo len dávkuje energiu zo závažia" pripisuje kyvadlu prácu, ktorú podľa zdroja robí krok (anker): ten riadi uvoľňovanie energie, kyvadlo určuje tempo. Veta je v rozpore s `b07`.
   Oprava: napísať, že energiu dávkuje krok a kyvadlo určuje tempo.

Verdict: RETURNED
