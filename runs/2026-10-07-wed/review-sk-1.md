# Slovenská kontrola — 2026-10-07-wed, kolo 1

Reviewed      runs/2026-10-07-wed/article.json, round 1
Commit        655e9b1d504610c907b1fd5ee0cfb7d81d513330
Environment   production
Checked at    2026-10-01T13:02+02:00

## Findings

1. **15/FAQ** · block `b27` (FAQ, 2. otázka „Treba pri stavbe lepidlo?“) · Odpoveď má štyri vety („Závisí od súpravy.“ / „Niektoré sa skladajú…“ / „Zoznam pomôcok nájdeš…“ / „Pri vlastnej výrobe…“). Widget dovoľuje odpoveď v jednej až troch vetách.
   Oprava: Skráť odpoveď na najviac tri vety, napr. spoj druhú a tretiu vetu; zvyšok nech zostane v rámci limitu 400 znakov.
2. **15/FAQ** · block `b27` (otázky 1 a 2) · Odpovede pridávajú nové tvrdenia, ktoré telo článku nemá: „scéna sa dá postaviť aj ako samostatná dekorácia na polici alebo vo vitríne“ (otázka 1) a „pri vlastnej výrobe z kartónu lepidlo potrebuješ takmer vždy“ (otázka 2). FAQ má odpoveď z tela len stručne zopakovať a nové tvrdenie nepridáva.
   Oprava: Buď tieto tvrdenia doplň do tela na vhodné miesto (napr. samostatné použitie do `b08`, lepidlo pri vlastnej výrobe do položky „Pomôcky“ v `b19`) a v FAQ ich len zopakuj, alebo ich z FAQ vypusti.
3. **09/E20** · block `b27` (otázka 2) · „lepidlo potrebuješ takmer vždy“ je všeobecné tvrdenie; zdroj (jennifermaker.com) opisuje jeden postup z lepenky, pri ktorom sa používa tavná pištoľ, nie pravidlo „takmer vždy“.
   Oprava: Nahraď tvrdením, ktoré zdroj potvrdzuje (napr. „pri postupe z lepenky sa diely lepia tavnou pištoľou“), alebo formuláciu s „takmer vždy“ vypusti.
4. **09/E25** · block `b11`, tretia položka zoznamu („Zrkadlo vzadu“) · Veta o zrkadle na zadnej stene sa v druhej vete zmení na „priesvitné zrkadlo“, ktoré sa nemá dávať úplne dopredu. Pojem „priesvitné zrkadlo“ nie je vysvetlený a čitateľ nevie, že ide o druhé zrkadlo pred zrkadlom vzadu. Odbornému termínu pri prvom použití chýba jedna veta vysvetlenia.
   Oprava: Vysvetli v jednej-dvoch vetách, že efekt nekonečnej cesty používa obyčajné zrkadlo vzadu a priesvitné (jednosmerné) zrkadlo pred ním, cez ktoré vidno dnu; a že priesvitné zrkadlo sa nedáva úplne dopredu, lebo by odrážalo svetlo z izby (zdroj jennifermaker.com to potvrdzuje).

Verdict: RETURNED
