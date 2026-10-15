# Karavan Hold'em

Texas Hold'em (No-Limit) pro partu na mobilech. Jeden telefon založí stůl, ostatní se připojí čtyřpísmenným kódem. Zprávy mezi telefony běží přes veřejné MQTT brokery (broker.emqx.io a broker.hivemq.com současně, kvůli záloze). Není potřeba vlastní server ani účet.

Hra: https://janzahradka.github.io/karavan-holdem/

- `index.html` – celá aplikace v jednom souboru (UI, herní engine, knihovna MQTT.js 5.10.4).
- Stav hry a zůstatky ukládá telefon hostitele (localStorage).
- Test proti lokálnímu brokeru: `index.html?broker=ws://127.0.0.1:8899`.

## Offline režim (bez internetu, např. v letadle)

- Jedno zařízení zapne Wi-Fi hotspot a nehraje (na zařízení s vlastním hotspotem WebRTC v Androidu nefunguje v Chrome ani Firefoxu). Hráči se k hotspotu připojí přes Wi-Fi.
- Hostitel zvolí *Založit offline stůl* (nebo *Pokračovat offline v uloženém stole*), hráči *Připojit se offline*.
- Párování: hráč naskenuje QR kód hostitele, hostitel naskenuje QR kód hráče. Pak se hraje přes WebRTC v místní síti.
- Aplikaci je potřeba aspoň jednou otevřít s internetem, aby se uložila do telefonu (service worker).
- Knihovny v `index.html`: MQTT.js 5.10.4, qrcode-generator 1.4.4, jsQR 1.4.0.
