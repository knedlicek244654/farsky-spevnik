# Farský spevník

Appka na zobrazovanie textov piesní na TV počas omše, ovládaná z tabletu. Napísaná v čistom Node.js – **žiadne npm balíčky, žiadna inštalácia**, stačí mať nainštalovaný Node.js.

## Štruktúra

```
farsky-spevnik/
├── server.js
├── README.md
├── data/
│   ├── jks.json         # zbierka JKS
│   ├── zalmy.json       # zbierka žalmov
│   ├── detsky.json      # detský spevník
│   ├── queue.json       # aktuálna fronta na omšu (vytvorí sa automaticky)
│   └── theme.json       # zvolená farebná predvoľba (vytvorí sa automaticky)
└── public/
    ├── home.html         # úvodná stránka s výberom
    ├── tablet.html       # ovládač (otvára sa na tablete)
    ├── tv.html           # zobrazenie (otvára sa na TV)
    └── admin.html        # administrácia (piesne, farby) – vyžaduje prihlásenie
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

Server počúva na `0.0.0.0`, takže je rovno dostupný aj z ostatných zariadení v tej istej WiFi sieti (tablet, TV) – netreba žiadne ďalšie nastavenia.

## Prístup k jednotlivým stránkam

Po otvorení `http://localhost:3800/` (alebo IP adresy počítača) ťa appka automaticky presmeruje na **úvodnú stránku** `/home` s tromi veľkými dlaždicami na výber:

| Stránka | Adresa | Na čo slúži |
|---|---|---|
| **Ovládač** | `/tablet` | výber a spúšťanie piesní – otvor na tablete |
| **TV zobrazenie** | `/tv` | zobrazenie textu piesne – otvor na obrazovke pri TV, cez F11 na celú obrazovku |
| **Administrácia** | `/admin` | pridávanie piesní a zmena vzhľadu TV – vyžaduje prihlásenie |

Na tablete/TV zisti lokálnu IP počítača príkazom `ipconfig` (Windows) alebo `ifconfig` / `ip a` (Mac/Linux), napr. `http://192.168.1.20:3800/home`.

## Ovládač (`/tablet`)

- Hore záložky na prepínanie medzi zbierkami (JKS, Žalmy, Detský spevník, ...).
- Vľavo klávesnica – zadaním čísla a tlačidlom **„Zobraziť na TV"** (alebo klávesou OK) pošleš pieseň na TV **okamžite**, mimo fronty. Hodí sa na spontánnu pieseň. Tlačidlom **„Skryť z TV"** sa TV vráti na pokojnú čakaciu obrazovku.
- V strede zoznam/vyhľadávanie piesní podľa čísla alebo názvu, s tlačidlom **„+“** na pridanie do fronty.
- Vpravo **fronta na omšu** (pozri nižšie).

### Fronta na omšu

Pred omšou si v strednom zozname tlačidlom **„+“** povyberáš piesne, ktoré chceš hrať (aj naprieč zbierkami) – zoradia sa vo fronte v poradí pridávania. Vo fronte môžeš:

- kliknutím na položku ju **rovno pustiť na TV**,
- šípkami **↑ / ↓** zmeniť poradie,
- krížikom **✕** ju odstrániť,
- tlačidlami **‹ / ›** hore prejsť na predchádzajúcu/nasledujúcu pieseň (počas omše tak stačí len klikať ›),
- tlačidlom **„Vymazať frontu"** ju celú vynulovať.

Fronta sa ukladá na serveri (`data/queue.json`), takže ostáva zachovaná aj pri obnovení stránky alebo reštarte servera – pokojne si ju priprav aj deň vopred.

## TV zobrazenie (`/tv`)

- Zobrazuje sa ako čistá stránka spevníka (bez rušivého orámovania) – pozadie má farbu podľa aktuálne zvolenej predvoľby.
- Text sa **automaticky prispôsobí dĺžke**: krátky text (napr. jedna sloha – typicky žalm) sa zobrazí vycentrovaný a veľký; dlhší text sa rozloží do 1–3 stĺpcov a písmo sa zmenší tak, aby sa vždy zmestilo celé, bez orezania.
- Aktualizuje sa okamžite (bez obnovovania stránky) cez server-sent events – zmena na ovládači alebo v administrácii sa na TV prejaví hneď.

## Administrácia (`/admin`)

Pri otvorení `/admin` sa prehliadač spýta na meno a heslo (natívne okienko, tzv. Basic Auth):

```
meno:  admin
heslo: admin
```

Prihlasovacie údaje sú nastavené v `server.js` (premenné `ADMIN_USER`, `ADMIN_PASS` na začiatku súboru) – ak chceš iné, stačí ich tam zmeniť a reštartovať server. Prihlásenie sa vyžaduje **len pri otvorení stránky** – samotné úpravy (pridanie piesne, zmena motívu) už heslo nevyžadujú. V hlavičke je červené tlačidlo **„Odhlásiť sa"**, ktoré ťa vráti na `/home` a pri ďalšom otvorení `/admin` si prehliadač znova vypýta heslo.

### Piesne

Pridávanie, úprava aj mazanie piesní vo všetkých zbierkach cez formulár (číslo, názov, sloha po slohe) – netreba ručne upravovať JSON súbory. Zmeny sa ukladajú priamo do príslušného súboru v `data/` a ak práve upravovaná/mazaná pieseň beží na TV, aktualizuje sa aj tam naživo.

### Vzhľad TV – 7 farebných predvolieb

| Predvoľba | Vzhľad |
|---|---|
| Slávnostná | biele pozadie, zlatý akcent |
| Cezročné obdobie | tmavozelené pozadie |
| Pôst a Advent | tmavofialové pozadie |
| Klasická | tmavomodré pozadie, zlatý akcent |
| Mučeníci a Turíce | tmavočervené pozadie |
| Jednoduchá | biele pozadie, čierny text |
| Jednoduchá invertovaná | čierne pozadie, biely text |

Zmena sa na TV prejaví okamžite, bez obnovenia stránky. Zvolená predvoľba sa ukladá do `data/theme.json`, takže prežije aj reštart servera.

## Pridanie skutočných piesní

Piesne sú v `data/jks.json`, `data/zalmy.json` a `data/detsky.json`. Najjednoduchšie ich pridáš cez `/admin`, prípadne priamo úpravou JSON súboru vo formáte:

```json
"125": {
  "title": "Názov piesne",
  "verses": [
    "Prvá sloha,\nriadok po riadku.",
    "Druhá sloha."
  ]
}
```

Kľúč (`"125"`) je číslo piesne/žalmu. Po ručnej úprave súboru stačí obnoviť `/tablet` alebo `/admin` – server netreba reštartovať.

Momentálne sú v súboroch len ukážkové položky s placeholder textom. Ak zbierka (najmä JKS) podlieha autorským právam, over si, či máte licenciu na verejné zobrazovanie textu (v mnohých farnostiach sa na to používa CCLI alebo podobná licencia).

## Pridanie ďalšej zbierky

1. Vytvor `data/nazov.json` v rovnakom formáte ako existujúce zbierky.
2. V `server.js` pridaj riadok do poľa `COLLECTIONS`:
   ```js
   { id: 'mladeznicky', name: 'Mládežnícky spevník', file: 'mladeznicky.json' },
   ```
3. Reštartuj server – nová záložka sa objaví automaticky na ovládači aj v administrácii.

## Ako to funguje (prehľad súborov)

- `server.js` – server bez akýchkoľvek npm balíčkov: stránky, API pre piesne, frontu, motívy a jednoduché prihlásenie do administrácie
- `public/home.html` – úvodná stránka s výberom Ovládač / TV / Administrácia
- `public/tablet.html` – ovládací panel: záložky zbierok, klávesnica, vyhľadávanie, fronta
- `public/tv.html` – zobrazenie pre TV, live aktualizácia cez server-sent events, automatické prispôsobenie textu aj farebného motívu
- `public/admin.html` – správa piesní a farebných predvolieb, chránená heslom
- `data/*.json` – databázy piesní jednotlivých zbierok, fronta a zvolený motív
