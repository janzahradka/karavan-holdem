# Hra proti počítači – fáze 2

## Cíl fáze
Boti se rozhodují podle síly ruky, ceny dorovnání a pozice na úrovni rekreačního hráče. Mimo blok botů se kód neměnil, online a offline hra se živými hráči zůstává beze změny.

## Jak bot rozhoduje
1. **Síla ruky (equity):** Monte Carlo simulace, 400 dorozdání před flopem a 500 po něm. Bot vidí jen svoje karty a stůl.
2. **Soupeři v simulaci:**
   - Bez sázky: všichni soupeři ve hře, s jakoukoli rukou.
   - Proti sázce: jen ti, kdo do ní už vložili. Hráči za botem většinou složí, a proto za každého z nich přibude k potřebné equity 2 procentní body.
   - Před flopem: soupeř, který zvýšil, dostává ruce z horních X % startovních rukou. X ≈ 0,9 / (zvýšení v BB), tedy 8–45 %. Žebříček rukou je podle Chenova vzorce.
   - Po flopu: proti sázce se equity krátí až o 30 % podle velikosti sázky vůči banku před ní.
3. **Pot odds:** potřebná equity = dorovnání / (bank + dorovnání + 40 % dorovnání za každého hráče, který ještě může dorovnat). Před riverem ×0,85 kvůli dalším kolům. K tomu přirážky za riziko velké části stacku (až +12 b.), před flopem +9 b., po flopu +3 b. a sleva za pozdní pozici (až −3 b.).
4. **Akce:**
   - Bez sázky: se silnou rukou sází v 75 % případů, se střední ve 45–60 %. Bluf se základní četností 10 %, častěji v pozdní pozici a proti 1–2 soupeřům.
   - Proti sázce: složí, když equity nestačí. Výjimkou je vzácný bluff-raise, jen když stojí méně než čtvrtinu stacku. Před flopem s dobrou rukou otevře zvýšením, s velmi silnou rukou zvyšuje i proti sázce, jinak dorovná.
   - Velikost sázek: 40–100 % banku podle situace, zaokrouhleno na malý blind.
5. **Chyba odhadu:** k equity se přičte náhodná odchylka ±5 b. Rozhodnutí proto nejsou deterministická.

Povaha bota je v parametrech `BOT_BASE` (`tight`, `aggr`, `bluff`, `noise`) a lze ji přepsat přes `player.persona`. Ve fázi 2 mají všichni boti neutrální hodnoty, osobnosti přijdou ve fázi 3.

## Technické
- Nový 7kartový vyhodnocovač `botEval` (bitové masky) je použitý jen pro simulace botů. Výsledky her dál počítá původní `bestScore`.
  - Shoda s `bestScore` ověřena na 200 000 náhodných dvojicích rukou (pořadí i kategorie): 0 rozdílů.
  - Rychlost 2–3 ms na 350 simulací, původní vyhodnocení potřebovalo 100–250 ms.
- Průměrné rozhodnutí trvá ~1 ms v Node. Na telefonu odhadem do 10 ms, takže UI nezamrzá.

## Ověření
**Simulace 4 000 her, 4 noví boti, pevné blindy:**

| Metrika | Výsledek | Cíl (rekreační hráč, 4 u stolu) |
|---|---|---|
| VPIP (hraje do banku před flopem) | 40–41 % | 35–42 % |
| PFR (zvyšuje před flopem) | 11–12 % | 10–18 % |
| AF po flopu (sázky+zvýšení / dorovnání) | 1,14–1,22 | 1–1,5 |
| WTSD (dojde do vyhodnocení) | 30 % | ~30 % |
| Neplatné akce | 0 | 0 |

**Nový bot proti botu z fáze 1:** +87 až +316 BB na 100 her (bot z fáze 1 −190 až −210).

**Modelové situace (200 opakování):**

| Situace | Rozhodnutí |
|---|---|
| AA první na tahu | zvýší 100 % |
| 72o první na tahu | složí 99 % |
| KQs první na tahu | zvýší 54 %, dorovná 25 %, složí 22 % |
| AK proti zvýšení na 5 BB | dorovná 100 % |
| J4o proti zvýšení na 5 BB | složí 98 % |
| Top set na flopu, první na tahu | vsadí 76 % |
| Vzduch na flopu, první na tahu | čeká 97 %, bluf 3 % |
| Vzduch na riveru proti sázce do banku | složí 100 % |
| Top pár na riveru proti ½ banku | dorovná 100 % |
| Nut flush proti all-in | dorovná 100 % |
| Malý pár proti all-in | složí 100 % |
| Flush draw proti ½ banku na flopu | dorovná 100 % |

**Prohlížeč:** sólo hra, reload, lobby, online stůl a přidání bota bez chyb.

## Známá omezení
- Bot si nepamatuje, jak soupeř hrál v minulých hrách, ani akce z předchozích kol téže hry. Rozsah zužuje jen podle aktuální sázky.
- 3-bet (zvýšení proti zvýšení) dělá jen s nejsilnějšími páry.
- Všichni boti zatím hrají stejným stylem.

## K ručnímu ověření
1. Odehrát 15–20 her proti počítači. Hodnotí se, jestli je hra „uvěřitelná“: neskládají všechno, nedorovnávají všechno a bluf je občas k vidění.
2. Online stůl se živým hráčem: beze změny.

## Další fáze
Fáze 3: osobnosti botů (rock, loose-aggressive, calling station…), celková obtížnost, tilt a délka přemýšlení podle obtížnosti rozhodnutí.
