# Slovenská kontrola — 2026-09-30-wed, kolo 1

Reviewed      runs/2026-09-30-wed/article.json, round 1
Commit        f618711c5fd264abea08360169a54cc68c678f88
Environment   development
Checked at    2026-09-30T19:15+02:00

## Findings

1. **01/What you must not do** · blocks `b14` (položka 4), `b25` (otázky 2 a 3), `b21` (položky 1 a 2) · Text je blízky preklad viet a poradia zo zdroja ROKR „Sand and Wax Your 3D Wooden Puzzle“ (pravidlo „Never copy“, platí aj pre preklad). `b14`: „pár ťahov brúsnym papierom je bezpečnejších než tlačenie, pri ktorom sa tenký čap ohne alebo zlomí“ zodpovedá zdroju „A few careful sanding strokes are safer than pushing until a tab bends or breaks“. `b25`: otázka „Musím voskovať všetky diely?“ s odpoveďou „Nie. Voskuj len pohyblivé miesta … alebo miesta, ktoré označuje návod. Vosk na všetkých dieloch môže zhoršiť …“ kopíruje zdrojovú otázku „Do all 3D wooden puzzle pieces need wax?“ aj jej odpoveď; otázka „Čím voskovať, keď doma nemám včelí vosk?“ s odpoveďou o sviečke zodpovedá zdrojovej otázke „What can I use if my kit does not include wax?“. `b21`: položky „obrús hneď po vylomení z dosky“ a „voskuj ešte pred uzavretím mechanizmu, keď sa k nim dá ľahko dostať“ idú v poradí a slovami zdroja („right after removing it from the board“, „easier to reach … before the mechanism is fully enclosed“).
   Oprava: Fakty (vosk len na pohyblivé miesta, sviečka ako náhrada, opatrnosť pri tesnom dieli) ostávajú, ale napíš ich vlastnými slovami a vlastnou štruktúrou. Tri otázky FAQ vymeň za otázky čitateľa, ktoré zdroj nemá (napr. čo robiť, keď sa po voskovaní mechanizmus stále zasekáva); `b14` položku 4 aj `b21` položky 1 a 2 preformuluj z vlastného postupu, nie z vety zdroja.
2. **14/Sidecar fields** · `products_used[0].used_in` · Produkt 52 je v texte aj v bloku `b22` (názov a odkaz v odseku), ale `used_in` obsahuje iba `b23`.
   Oprava: Do `used_in` doplň `b22` (po úprave textu skontroluj, že `used_in` zodpovedá všetkým blokom, kde sa produkt objavuje).
3. **09/E20** · blocks `b09` (riadok „Včelí vosk alebo sviečka“, stĺpec „Kam sa hodí“) a `b25` (odpoveď na otázku 2) · Tvrdenie, že vosk patrí aj do „otvorov pre osi“, nepodporuje žiadny zdroj v `sources`; zdroj ROKR spomína ozubené kolesá, pánty, posuvné časti a miesta označené v návode.
   Oprava: Vyraď „otvory pre osi“ v oboch blokoch, alebo pridaj zdroj, ktorý to potvrdzuje, a zapíš ho do `sources`.
4. **09/E20** · block `b09` (riadok „Mäkká ceruzka, napríklad 4B“) · Jediný zdroj (cabaret.co.uk, „Dug’s Tips 1“) ceruzku ako mazivo opisuje, no komentár pod tým istým článkom upozorňuje, že ceruzková „tuha“ obsahuje abrazívnu hlinku, ktorá môže ložiská opotrebovať; článok to neuvádza a tvrdenie podáva bez výhrady. „4B“ zdroj nemenuje (uvádza len „číslo a písmeno B“).
   Oprava: Riadok s ceruzkou vyraď, alebo ho napíš s výhradou, ktorú zdroj uvádza, a bez neoverenej konkrétnej hodnoty „4B“; zdroj ponechaj v `sources` len pre to, čo naozaj potvrdzuje.
5. **09/E25** · field `seo_description` · Fráza „čím trenie znížiť z vecí z domu“ je nesprávna väzba a nespisovná („znížiť z vecí“, „z domu“).
   Oprava: Napíš napr. „Ako nájsť miesto, kde sa v drevenom modeli diely trú, čím trenie znížiť pomocou vecí z domácnosti a kedy ide o zle usadený diel. Návod pre prvý model.“ (150 znakov; drž sa 120 – 155).
6. **09/E25** · blocks `b06`, `b11`, `b25` (odpoveď na otázku 2) · „sedenie dielov“ je kalk z angl. „fit“ a v slovenčine neznie prirodzene („môže sedenie dielov skôr zhoršiť“, „zhoršiť sedenie dielov“).
   Oprava: Nahraď prirodzeným slovným spojením, napr. „diely potom zapadajú horšie“ alebo „zhorší presnosť spojov“.
7. **09/E23** · field `cover.file` (cover.png) a `cover.prompt` · Obrázok nezodpovedá promptu: prompt žiada „soft hand-painted digital illustration“, súbor vyzerá ako fotorealistická snímka (drevo, pokožka rúk, plytká hĺbka ostrosti). Fotorealistický obrázok s textom „ilustračný“ môže čitateľ pokladať za skutočnú fotografiu, nie za ilustráciu. Sviečka z promptu na obrázku nie je zreteľne rozpoznateľná (vidno skôr drobný nástroj s kovovým hrotom). Rozmer 1280 × 720 a absencia textu, loga, čísel a tvárí vyhovujú.
   Oprava: Vygeneruj obálku znova tak, aby bola zjavne maľovaná alebo kreslená ilustrácia (bez fotorealizmu), a aby zodpovedala `cover.prompt`; prípadne uprav `cover.prompt` podľa nového súboru.

Verdict: RETURNED
