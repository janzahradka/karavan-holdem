# Hra proti počítači – fáze 1

## Cíl fáze
Režim pro jednoho hráče proti počítačovým soupeřům, zatím s jednoduchým rozhodováním. Online a offline hra se živými hráči beze změny chování.

## Architektura
- Boti jsou běžní hráči v `HS.players` s příznakem `bot: true`. Běží jen u hostitele a hrají přes stejný `applyCmd(..., 'act', ...)` jako lidé. Engine (`startHand`, `act`, `progress`, `showdown`) se neměnil.
- Bot rozhoduje z `viewFor(botId)`, tedy jen z toho, co by viděl hráč na jeho místě. Cizí karty a balíček jsou skryté.
- Sólo hra používá `SoloBus`: síťovou vrstvu, která nic neposílá. Režim se předává jako `startHost(code, resume, 'solo')`. Dosavadní volání s `true`/`false` (offline/online) fungují jako dřív.

## Změny v `index.html`
| Oblast | Změna |
|---|---|
| Nový blok skriptu | `botDecide(view, id)`, `botLegal`, `botSize`, `BOT_NAMES`. Bez DOM, testovatelné v Node. |
| Spojení | `SoloBus`, `isSolo()` |
| Hostitel | `addBot`, plánovač `botSchedule()` (volá se po každém `broadcast`), příkaz `addbot` (jen hostitel), boti se počítají mezi připojené, rozdat za bota smí kdokoli |
| `startHost` | třetí režim `'solo'`, `HS.solo = true`, seznam připojených se počítá hned (dřív až po 4 s) |
| Lobby | karta „Hrát sám proti počítači“ (3 boti), uložený sólo stůl má tlačítko „Hrát proti počítači“ |
| Stůl | štítek PC u botů, tlačítko „Rozdat za &lt;bot&gt;“ |
| Záložka Hráči | „Přidat počítačového hráče“ pro hostitele ve všech režimech (online, offline i sólo) |
| Zvuk | v sólo hře hraje vždy (kromě „Zvuk: vyp“) |
| `sw.js` | cache `kh-v9` |

## Chování botů (dočasné, nahradí fáze 2)
- Když není co dorovnávat: 25 % vsadí 40–100 % banku, jinak čeká.
- Když je co dorovnávat: pravděpodobnost složení roste s tím, jakou část stacku sázka stojí (12–85 %). Jinak dorovná a v 10 % případů zvýší.
- Na karty se zatím nedívají.
- Před tahem počkají, až doběhne oznámení předchozí akce, pak 0,9–2,5 s „přemýšlejí“.
- Bez žetonů si mezi hrami dokoupí vklad.
- Pojistka: kdyby rozhodnutí engine odmítl, bot čeká nebo složí.

## Ověření
- Simulace 3 000 her bot proti botům přes skutečný engine: 0 neplatných akcí, žádné zaseknutí, součet žetonů sedí po každé hře.
- Prohlížeč (Chromium, 390 × 800): sólo stůl se 3 boty, 6 odehraných her, obnovení po reloadu, seznam stolů v lobby, běžný online stůl (MQTT) se zakládá jako dřív, přidání bota k online stolu. Bez chyb v konzoli.
- Neověřeno: reálné spojení se živými telefony (online i offline). Kód spojení se neměnil.

## K ručnímu ověření
1. Lobby → „Hrát proti počítači“: 3 soupeři se štítkem PC, hra plyne bez zásahu kromě vlastních tahů a rozdání.
2. Reload stránky uprostřed hry: stůl i rozehraná hra pokračují.
3. Online stůl se živým hráčem: beze změny. Volitelně „Přidat počítačového hráče“.

## Další fáze
Fáze 2: síla ruky (preflop tabulka, Monte Carlo equity), pot odds, pozice.
