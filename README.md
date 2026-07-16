# Farský spevník – ovládač, TV zobrazenie a administrácia

Jednoduchá appka bez závislostí (čistý Node.js). Beží na `localhost:3800`.

## Štruktúra

```
farsky-spevnik/
├── server.js
├── README.md
├── data/
│   ├── jks.json         # zbierka JKS
│   ├── zalmy.json       # zbierka žalmov
│   ├── queue.json       # aktuálna fronta na omšu (vytvorí sa automaticky)
│   └── theme.json       # zvolená farebná predvoľba (vytvorí sa automaticky)
└── public/
    ├── tablet.html       # ovládač (otvára sa na tablete)
    ├── tv.html           # zobrazenie (otvára sa na TV)
    └── admin.html        # administrácia (pridávanie piesní, farby)
```

## Spustenie

```
node server.js
```

Zobrazí sa:
```
Farský spevník beží na http://localhost:3800
  Ovládač (tablet): http://localhost:3800/tablet
  Zobrazenie (TV):  http://localhost:3800/tv
```

Administrácia je na `http://localhost:3800/admin`.

## Použitie

- **TV**: `http://localhost:3800/tv`, na celú obrazovku (F11).
- **Tablet**: v tej istej WiFi sieti `http://<IP počítača>:3800/tablet`. Lokálnu IP zistíš príkazom `ipconfig` (Windows) alebo `ifconfig` / `ip a` (Mac/Linux).
- **Administrácia**: `http://localhost:3800/admin` – z počítača alebo pokojne aj z tabletu/telefónu v tej istej sieti.

### Fronta na omšu

V ovládači (`/tablet`) je tretí, úzky panel **„Fronta na omšu“**. Piesne pridávaš tlačidlom **„+“** priamo v zozname (funguje naprieč zbierkami). Vo fronte vieš poradie meniť šípkami, mazať krížikom, alebo len klikať položky pre okamžité zobrazenie na TV. Fronta sa ukladá na serveri (`data/queue.json`), takže prežije reštart aj obnovenie stránky.

### Administrácia (`/admin`)

- **Piesne** – pridávanie, úprava a mazanie piesní v oboch zbierkach cez formulár (číslo, názov, sloha po slohe) – netreba ručne upravovať JSON súbory.
- **Vzhľad TV** – 5 farebných predvolieb (Slávnostná, Cezročné obdobie, Pôst a Advent, Vianoce, Jednoduchá čiernobiela). Zmena sa prejaví na TV okamžite, bez obnovenia stránky.

Zmeny z administrácie sa ukladajú priamo do `data/jks.json`, `data/zalmy.json` a `data/theme.json`.

## Pridanie ďalšej zbierky

1. Vytvor `data/nazov.json` v rovnakom formáte ako `jks.json`.
2. V `server.js` pridaj riadok do poľa `COLLECTIONS`:
   ```js
   { id: 'detsky', name: 'Detský spevník', file: 'detsky.json' },
   ```
3. Reštartuj server – nová záložka sa objaví automaticky na ovládači aj v administrácii.

## Autorské práva

Piesne pridávané cez administráciu (najmä z JKS) môžu podliehať autorským právam. Over si, či máte licenciu na verejné zobrazovanie textu (v mnohých farnostiach na to slúži CCLI alebo podobná licencia).

## Ako to funguje

- `server.js` – server: stránky, API pre piesne, frontu, motívy aj administráciu (bez npm balíčkov)
- `public/tablet.html` – ovládací panel: záložky zbierok, klávesnica, vyhľadávanie, fronta
- `public/tv.html` – zobrazenie pre TV, aktualizuje sa okamžite cez server-sent events (SSE), aplikuje aj zvolený farebný motív
- `public/admin.html` – správa piesní a farebných predvolieb
- `data/*.json` – databázy piesní, fronta a zvolený motív
