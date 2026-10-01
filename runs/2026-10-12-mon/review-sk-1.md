# Slovenská kontrola — 2026-10-12-mon, kolo 1

Reviewed      runs/2026-10-12-mon/article.json, round 1
Commit        457744e57e601394c41938dafc36c0c5b13c2db3
Environment   production
Checked at    2026-10-01T14:48+02:00

## Findings

1. **09/E25** · block `b09` · Prvá veta znie neprirodzene: „Na drevo na drevo použi biele drevárske lepidlo“. Opakovanie „na drevo“ čitateľa zbytočne zastaví.
   Oprava: Prepíš na prirodzenú slovenčinu, napríklad „Na lepenie dreva na drevo použi biele drevárske lepidlo (PVA, teda bežné biele lepidlo na drevo).“
2. **09/E20** · block `b14` · Tip tvrdí, že minca „nezanechá stopy“. Zdroj (craftsandkits.com) hovorí iba, že závažie, napríklad minca, položené priamo na plochý spoj namiesto pásky sa vyhne stopám po páske. Tvrdenie je istejšie než zdroj.
   Oprava: Napíš to tak, ako to zdroj hovorí: „Plochý spoj môžeš namiesto pásky zaťažiť mincou, aby na dreve nezostali stopy po páske.“
3. **09/E27** · block `b20` · Krok „Ak je výstupok príliš tesný, zabrús ho jemným brúsnym papierom okolo hrubosti 220“ odoberá materiál z dielu a je napísaný ako všeobecný zásah bez odkazu na návod; upozornenie na návod stojí až v poslednom odseku `b29` v inej časti.
   Oprava: Pridaj do kroku odkaz na návod, napríklad „Ak návod pri tomto diele radí inak, riaď sa ním; inak, ak je výstupok príliš tesný, zabrús ho …“, a zabrús len po skúške nasucho.
4. **09/E20** · block `b28` (šiesta položka) · „Výstupok sa láme v najtenšom mieste, tam, kde sa stretáva s drážkou.“ Zdroj (thereadingresidence.com) píše, že výstupok sa láme v najtenšom mieste, „ktoré je zvyčajne tam, kde sa stretáva so štrbinou“. Článok z „zvyčajne“ robí istotu.
   Oprava: Doplň obmedzenie: „Výstupok sa láme v najtenšom mieste, ktoré je zvyčajne tam, kde sa stretáva s drážkou.“
5. **09/E20** · block `b28` (prvá položka) · „Diel s vlásočnicovou trhlinou alebo napoly odtrhnutou spojkou vylamuj ako posledný.“ Poradie „ako posledný“ nie je v žiadnom zdroji; zdroj (thereadingresidence.com) radí len rozpoznať krehké diely včas a zaobchádzať s nimi opatrnejšie.
   Oprava: Vypusti „ako posledný“ a nechaj len to, čo zdroj podporuje (prezrieť dosku proti svetlu, krehký diel vylamovať mimoriadne opatrne), alebo pridaj zdroj, ktorý poradie potvrdzuje.
6. **09/Čo do článku nepatrí** (spec 01 „Never copy“) · blocks `b14`, `b17`, `b24`, `b25` · Postup čistého zlomu v `b17` má rovnaké kroky v rovnakom poradí aj s rovnakými podrobnosťami ako zdroj craftsandkits.com („Scenario 1: The Clean Break“: skúška nasucho, lepidlo na jednu plochu, stlačenie 10 až 15 sekúnd, zotretie vlhkou handričkou, páska, 30 minút, odstránenie pásky, tmel); `b14` kopíruje odsek o upínaní (páska v dvoch–troch vrstvách, svorky priveľké, minca), `b24` poradie údajov pre výrobcu (názov, objednávka, číslo dielu, fotografia strany návodu) a `b25` dvojicu „plochý diel z kartónu / stĺpik zo špáradla“. Fakty sa môžu zhodovať, no poradie krokov a ich zoskupenie z jedného zdroja sa kopírovať nesmie.
   Oprava: Prepíš `b17`, `b14`, `b24` a `b25` vlastným usporiadaním: spoj kroky podľa toho, čo čitateľ pozoruje (napr. „sadne bez medzery“ → „lepidlo“ → „fixácia“), zmeň zoskupenie a poradie podrobností a použi aspoň dva zdroje, ktoré sa na postupe zhodujú; fakty ponechaj, štruktúru nie.

Verdict: RETURNED
