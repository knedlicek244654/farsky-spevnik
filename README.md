# Farský spevník – ovládač a TV zobrazenie

Jednoduchá appka bez závislostí (čistý Node.js). Beží na `localhost:3800`.
Obsahuje dve zbierky: **JKS** a **Žalmy**, a **frontu piesní na omšu**.

## Štruktúra

```
farsky-spevnik/
├── server.js
├── README.md
├── data/
│   ├── jks.json         # zbierka JKS
│   ├── zalmy.json       # zbierka žalmov
│   └── queue.json       # aktuálna fronta na omšu (vytvorí sa automaticky)
└── public/
    ├── tablet.html       # ovládač (otvára sa na tablete)
    └── tv.html           # zobrazenie (otvára sa na TV)
```

## Spustenie

1. Rozbaľ priečinok `farsky-spevnik` na počítač, ktorý bude pripojený k TV.
2. V termináli v tomto priečinku spusti:
   ```
   node server.js
   ```
3. Zobrazí sa:
   ```
   Farský spevník beží na http://localhost:3800
   ```

## Použitie

- **Na TV**: otvor `http://localhost:3800/tv`, daj na celú obrazovku (F11). Zobrazuje sa ako biela stránka spevníka.
- **Na tablete**: v tej istej WiFi sieti otvor `http://<IP počítača>:3800/tablet` (napr. `http://192.168.1.20:3800/tablet`). Lokálnu IP zistíš príkazom `ipconfig` (Windows) alebo `ifconfig` / `ip a` (Mac/Linux).

### Príprava pred omšou – fronta

Vpravo je tretí, úzky panel **„Fronta na omšu“**. V zozname piesní (prostredný panel) má každá pieseň tlačidlo **„+“** – tým ju pridáš do fronty v poradí, v akom ju chceš hrať. Piesne môžeš pridávať z oboch zbierok (JKS aj Žalmy) do jednej spoločnej fronty.

Vo fronte môžeš:
- kliknutím na položku ju **rovno pustiť na TV**,
- šípkami **↑ / ↓** zmeniť poradie,
- krížikom **✕** ju odstrániť,
- tlačidlami **‹ / ›** hore prejsť na predchádzajúcu/nasledujúcu pieseň vo fronte (počas omše tak stačí len klikať ›),
- tlačidlom **„Vymazať frontu“** ju celú vynulovať.

Fronta sa ukladá na server (`data/queue.json`), takže ostáva zachovaná aj pri obnovení stránky alebo reštarte servera – pokojne si ju priprav deň vopred.

### Priame zobrazenie mimo fronty

Vľavo je klávesnica – zadaním čísla a tlačidlom **„Zobraziť na TV“** (alebo klávesou OK) pošleš pieseň na TV okamžite, bez pridania do fronty. Hodí sa na spontánnu/nečakanú pieseň. Tlačidlom **„Skryť z TV“** sa TV vráti na pokojnú čakaciu obrazovku.

## Pridanie skutočných piesní

Piesne sú v `data/jks.json` a `data/zalmy.json`, formát:

```json
"125": {
  "title": "Názov piesne",
  "verses": [
    "Prvá sloha,\nriadok po riadku.",
    "Druhá sloha."
  ]
}
```

Kľúč (`"125"`) je číslo piesne/žalmu. Po úprave stačí obnoviť `/tablet` – server netreba reštartovať.

Momentálne sú tam len ukážkové položky s placeholder textom – nahraď ich reálnymi. Ak zbierka (napr. JKS) podlieha autorským právam, over si licenciu na verejné zobrazovanie textu (v mnohých farnostiach sa na to používa CCLI alebo podobná licencia).

## Pridanie ďalšej zbierky

1. Vytvor `data/nazov.json` v rovnakom formáte.
2. V `server.js` pridaj riadok do poľa `COLLECTIONS`:
   ```js
   { id: 'detsky', name: 'Detský spevník', file: 'detsky.json' },
   ```
3. Reštartuj server – nová záložka sa objaví automaticky na ovládači.

## Ako to funguje

- `server.js` – server, ktorý servíruje stránky, API pre piesne a frontu (bez npm balíčkov)
- `public/tablet.html` – ovládací panel: záložky zbierok, klávesnica, vyhľadávanie, fronta
- `public/tv.html` – zobrazenie pre TV, aktualizuje sa okamžite cez server-sent events (SSE)
- `data/*.json` – databázy piesní jednotlivých zbierok
- `data/queue.json` – aktuálna fronta na omšu (perzistentná)
