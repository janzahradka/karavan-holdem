# Karavan Hold'em

Texas Hold'em (No-Limit) pro partu na mobilech. Jeden telefon založí stůl, ostatní se připojí čtyřpísmenným kódem. Zprávy mezi telefony běží přes veřejné MQTT brokery (broker.emqx.io a broker.hivemq.com současně, kvůli záloze). Není potřeba vlastní server ani účet.

Hra: https://janzahradka.github.io/karavan-holdem/

- `index.html` – celá aplikace v jednom souboru (UI, herní engine, knihovna MQTT.js 5.10.4).
- Stav hry a zůstatky ukládá telefon hostitele (localStorage).
- Test proti lokálnímu brokeru: `index.html?broker=ws://127.0.0.1:8899`.
