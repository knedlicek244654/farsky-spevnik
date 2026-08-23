# Farský spevník - webová edícia

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
│   ├── collections.json # zoznam všetkých zbierok vrátane novo pridaných (vytvorí sa automaticky)
│   ├── queue.json       # aktuálna fronta na omšu (vytvorí sa automaticky)
│   ├── favorites.json   # obľúbené piesne pre rýchly prístup na ovládači (vytvorí sa automaticky)
│   ├── theme.json       # zvolená farebná predvoľba, automatický režim a obdobia (vytvorí sa automaticky)
│   ├── idle-images.json # zoznam obrázkov pre čakaciu obrazovku (vytvorí sa automaticky)
│   ├── auth.json        # zahashované heslo do administrácie (vytvorí sa automaticky)
│   └── history.json     # história hraných piesní (vytvorí sa automaticky)
└── public/
    ├── home.html         # úvodná stránka s výberom
    ├── tablet.html       # ovládač (otvára sa na tablete)
    ├── tv.html           # zobrazenie (otvára sa na TV)
    ├── admin.html        # administrácia (piesne, farby, obrázky, heslo, história) – vyžaduje prihlásenie
    ├── admin-login.html  # vlastná prihlasovacia stránka do administrácie
    └── idle-images/      # nahrané obrázky pre čakaciu obrazovku (vytvorí sa automaticky)
```

## Spustenie

```
node server.js
```

Zobrazí sa:
```
Farský spevník beží na http://localhost:3800
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
- Vľavo klávesnica – zadaním čísla (voliteľne s čiarkou a číslom slohy, napr. `125,3`) a tlačidlom **„Zobraziť na TV"** (alebo klávesou Enter) pošleš pieseň na TV **okamžite**, mimo fronty. Hodí sa na spontánnu pieseň. Pod tým tlačidlá **„◀ Späť"** / **„Ďalej ▶"** posúvajú TV medzi jednotlivými slohami práve zobrazenej piesne (TV vždy ukazuje len jednu slohu naraz), s ukazovateľom „2 / 4". Pod tým tlačidlá **„A− Menšie"** / **„⟲"** / **„A+ Väčšie"** ručne upravia veľkosť textu na TV nad rámec toho, čo by zvolila automatika – hodí sa, ak by chcel niekto väčšie alebo menšie písmo než automat vyberie; „⟲" vráti späť na čistú automatiku. TV pritom vždy stráži, aby sa text nikdy neorezal mimo obrazovky – ak by pri „A+ Väčšie" text prestal byť celý viditeľný, mierne ho sám dozmenší, aby zostal celý čitateľný. Nastavenie sa resetuje pri každej novej piesni. Tlačidlom **„Skryť z TV"** sa TV vráti na čakaciu obrazovku s nahratým obrázkom (ak nejaký nahraný je).

### Ovládanie klávesnicou / numerickou klávesnicou (numpad)

Appku vieš ovládať prakticky celú aj len z numerickej klávesnice (numpad), bez potreby myši:

| Klávesa | Akcia |
|---|---|
| `0`–`9` | Zadať číslicu čísla piesne (funguje aj z numpadu pri zapnutom NumLocku) — automaticky prepne výber späť na zoznam piesní |
| `,` (čiarka) | Oddelovač slohy — napr. `125,3` zobrazí na TV rovno 3. slohu piesne č. 125 (funguje aj z numpadu) |
| `Enter` | Potvrdiť napísané číslo a zobraziť na TV; ak nie je napísané žiadne číslo, zobrazí OZNAČENÚ pieseň zo zoznamu (šípkami); keď je výber vo fronte (pozri „Insert" nižšie), namiesto toho prehrá vybranú položku z fronty |
| `Delete` | Vymazať celé zadané číslo; ak je pole prázdne a si vo fronte (pozri `Insert`), vymaže **celú frontu** (rovnaké potvrdenie ako tlačidlo „Vymazať frontu"); na záložke Obľúbené odoberie OZNAČENÚ pieseň |
| `Backspace` | Zmazať poslednú zadanú číslicu; ak je pole prázdne, odstráni len OZNAČENÚ položku (vo fronte len tú jednu, nikdy celú frontu) |
| `Insert` | Prepnúť, kam mieria šípky `↑`/`↓` — medzi zoznamom piesní a frontou (a naspäť) |
| `↑` / `↓` | Označiť (rámikom) predchádzajúcu/nasledujúcu položku — v zozname piesní, alebo vo fronte (podľa toho, kam práve mieria šípky, pozri „Insert") |
| `←` | Predchádzajúca sloha práve zobrazenej piesne na TV |
| `→` | Nasledujúca sloha práve zobrazenej piesne na TV |
| `*` | Pridať/odobrať práve označenú položku z obľúbených — funguje pre označenú pieseň v zozname aj pre označenú položku vo fronte |
| `+` | V zozname piesní pridá označenú pieseň do fronty; vo fronte namiesto toho rovno prehrá označenú položku na TV |
| `-` | Vo fronte odstráni označenú položku; na záložke „★ Obľúbené" odoberie označenú pieseň z obľúbených |
| `Home` | Skryť z TV |
| `Page Up` / `Page Down` | Prepnúť na predchádzajúcu/nasledujúcu zbierku (JKS ↔ Žalmy ↔ Detský spevník) |

Skratky sú neaktívne, pokiaľ píšeš do poľa vyhľadávania (aby sa neprepisovalo bežné písanie). Klávesa `Insert` prepína, ktorý panel je práve „aktívny" pre šípky — aktívny panel má okolo seba jemný zlatý rámik. Príklad: otvoríš stránku → panel piesní je aktívny → `↓↓↓↓` označí 4. pieseň v zozname → `*` ju pridá do obľúbených, `+` do fronty. Stlačením `Insert` sa aktívnym stane panel fronty → `↓`/`↑` teraz vyberajú položky vo fronte → `Enter` alebo `+` ju rovno prehrá na TV, `-` ju z fronty odstráni. Napísanie čísla piesne (ktorákoľvek číslica) ťa automaticky prepne späť na panel piesní.

**Poznámka k numpadu:** väčšina numerických klávesníc pri **vypnutom NumLocku** posiela namiesto číslic `Insert` (na `0`), `Delete` (na desatinnej bodke/čiarke) a šípky (na `2`/`4`/`6`/`8`) — takže s numpadom vieš prepínať medzi zadávaním čísla (NumLock zapnutý) a navigáciou po zoznamoch/fronte (NumLock vypnutý) bez toho, aby si musel siahnuť na hlavnú klávesnicu. Klávesy `*`, `+` a `-` na numpade posielajú tieto znaky vždy, bez ohľadu na NumLock.

- V strede zoznam/vyhľadávanie piesní podľa čísla alebo názvu, s tlačidlom **„+“** na pridanie do fronty a hviezdičkou **☆/★** na pridanie/odobratie z obľúbených. Vyhľadávanie funguje **bez diakritiky** (napr. „pastier" nájde aj „Pastier") a keď niečo napíšeš, hľadá **naprieč všetkými zbierkami naraz** (nielen v aktuálne vybranej záložke) — výsledky z inej zbierky sú označené štítkom s jej názvom.
- Úplne prvá záložka **„★ Obľúbené"** funguje ako ďalšia zbierka — zobrazí zoznam všetkých obľúbených piesní naprieč zbierkami (s vyznačením, z ktorej zbierky pochádzajú), kliknutím na pieseň ju hneď pošleš na TV, tlačidlom **„✕"** ju z obľúbených odstrániš.
- Vpravo **fronta na omšu** (pozri nižšie).

### Fronta na omšu

Pred omšou si v strednom zozname tlačidlom **„+“** povyberáš piesne, ktoré chceš hrať (aj naprieč zbierkami) – zoradia sa vo fronte v poradí pridávania. Vo fronte môžeš:

- kliknutím na položku ju **rovno pustiť na TV**,
- tlačidlami **↑ / ↓ pri položke** zmeniť jej poradie,
- krížikom **✕** ju odstrániť,
- tlačidlami **‹ / ›** hore prejsť na predchádzajúcu/nasledujúcu pieseň (počas omše tak stačí len klikať ›),
- tlačidlom **„Vymazať frontu"** ju celú vynulovať.

Klávesou **Insert** vieš prepnúť šípky `↑`/`↓` na klávesnici tak, aby namiesto zoznamu piesní označovali (rámikom) položky priamo vo fronte — pozri tabuľku klávesových skratiek vyššie. Nezamieňaj s tlačidlami ↑/↓ priamo pri položke fronty, ktoré menia jej poradie.

Fronta sa ukladá na serveri (`data/queue.json`), takže ostáva zachovaná aj pri obnovení stránky alebo reštarte servera – pokojne si ju priprav aj deň vopred.

## TV zobrazenie (`/tv`)

- Zobrazuje sa ako čistá stránka spevníka (bez rušivého orámovania) – pozadie má farbu podľa aktuálne zvolenej predvoľby.
- Zobrazuje sa **vždy len jedna sloha naraz**, veľká a vycentrovaná, s ukazovateľom poradia (napr. „2 / 4"). Písmo sa automaticky zmenší, ak by sa aj samotná jedna sloha nezmestila celá na obrazovku.
- Medzi slohami sa prepína z ovládača tlačidlami **„◀ Späť"** / **„Ďalej ▶"** (pozri nižšie) – zmena sa na TV prejaví okamžite.
- Aktualizuje sa okamžite (bez obnovovania stránky) cez server-sent events – zmena na ovládači alebo v administrácii sa na TV prejaví hneď.

## Administrácia (`/admin`)

Pri otvorení `/admin` bez prihlásenia sa zobrazí vlastná prihlasovacia stránka v rovnakom dizajne ako zvyšok appky (nie natívne okienko prehliadača):

```
heslo: admin (predvolené — dá sa zmeniť, pozri nižšie)
```

Po zadaní správneho hesla appka vydá reláciu (cookie), ktorá platí 60 dní alebo do odhlásenia — netreba sa prihlasovať znova pri každom otvorení. Prihlásenie sa vyžaduje **len pri otvorení stránky** – samotné úpravy (pridanie piesne, zmena motívu) už heslo nevyžadujú. V hlavičke je červené tlačidlo **„Odhlásiť sa"**, ktoré reláciu zruší a vráti ťa na `/home`.

### Zmena hesla

Úplne dole na stránke je karta **„Zmena hesla"** (zabudovaná priamo do stránky, žiadne vyskakovacie okno) – zadáš súčasné heslo, nové heslo dvakrát pre kontrolu, a potvrdíš. Heslo sa ukladá zahashované (nie ako čitateľný text) do `data/auth.json`. Po zmene si pri ďalšom prihlásení zadáš už nové heslo.

### Piesne

Pridávanie, úprava aj mazanie piesní vo všetkých zbierkach cez formulár (číslo, názov, sloha po slohe) – netreba ručne upravovať JSON súbory. Zmeny sa ukladajú priamo do príslušného súboru v `data/` a ak práve upravovaná/mazaná pieseň beží na TV, aktualizuje sa aj tam naživo.

### Hromadné pridanie piesní

Tlačidlom **„📋 Hromadne"** (vedľa „+ Nová pieseň") otvoríš textové pole, kam vieš naraz vložiť celý zoznam piesní namiesto pridávania jednej po druhej. Formát:

```
125 | Názov piesne
Prvá sloha,
druhý riadok tej istej slohy

Druhá sloha
---
126 | Ďalšia pieseň
Text piesne...
```

- Prvý riadok každej piesne: **číslo | názov**.
- Ďalšie riadky: text piesne, jednotlivé slohy oddelené **prázdnym riadkom**.
- Jednotlivé piesne oddeľ riadkom s **`---`**.

Import pridá nové piesne a existujúce (podľa čísla) prepíše. Po importe appka napíše, koľko sa pridalo/aktualizovalo a upozorní, ak niektorý blok nevedela spracovať (napr. chýbajúce číslo alebo „|").

### Obrázky pre čakaciu obrazovku

Keď sa práve nehrá žiadna pieseň, TV zobrazuje čakaciu obrazovku s nahratým obrázkom na pozadí a jeho **názvom dole na strede** (napr. „Sv. Petra a Pavla"). Ak obrázok nemá vyplnený názov, nič sa dole nezobrazí. V administrácii ju vieš vyzdobiť vlastnými obrázkami na celý rok (napr. tematickými ku dňu, sviatku alebo liturgickému obdobiu):

- Tlačidlom **„+ Nahrať obrázok"** vyber súbor (JPG/PNG/WEBP/GIF, max. 8 MB) a hneď mu daj **názov** (napr. „December / Vianoce", „Marec / Pôst", „Turíce") – podľa neho ho potom nájdeš.
- Nad mriežkou obrázkov je pole **„Hľadať podľa názvu…"**, ktoré filtruje zoznam rovnako, ako vyhľadávanie piesní na ovládači.
- Názov obrázka vieš kedykoľvek zmeniť priamo v poli pod miniatúrou.
- Pod názvom sú dve malé polia **„Obdobie od–do"** (formát deň.mesiac, napr. `24.12` – `6.1`) – v tomto období sa obrázok vyberie **automaticky**, bez potreby ho ručne zvoliť. Obdobie smie prechádzať cez Nový rok (napr. `20.12` – `6.1`). Nechaj prázdne, ak obrázok nemá viazať na konkrétne obdobie.
- Krížikom obrázok odstrániš.
- Tlačidlom **„Zobraziť na TV"** pri konkrétnom obrázku ho **pevne zvolíš** – má vždy prednosť pred automatickým výberom podľa obdobia aj pred náhodným striedaním. Zvolený obrázok je označený zlatým rámikom a štítkom „Aktívne na TV". Opätovným kliknutím naň sa pevná voľba zruší. Zmena sa na TV prejaví okamžite.

Poradie výberu na TV: **1)** pevne zvolený obrázok (ak je), **2)** obrázok, ktorého obdobie sedí na dnešný dátum (ak je viac takých, vyberie sa náhodne spomedzi nich), **3)** úplne náhodný spomedzi všetkých (strieda sa asi každých 30 sekúnd). Bez nahraných obrázkov zostáva čakacia obrazovka jednoduchá, podľa zvoleného farebného motívu. Obrázky sa ukladajú do `public/idle-images/` (samotné súbory) a `data/idle-images.json` (zoznam s názvami, obdobiami aj zvoleným aktívnym obrázkom).

### Obľúbené piesne

V ovládači (`/tablet`) sa obľúbené piesne správajú ako ďalšia zbierka — záložka **„★ Obľúbené"** hneď na začiatku pruhu záložiek. Otvorí zoznam všetkých obľúbených piesní naprieč zbierkami (s vyznačením, z ktorej zbierky pochádzajú); kliknutím na pieseň ju hneď pošleš na TV, tlačidlom **„✕"** ju z obľúbených odstrániš. V bežných zbierkach pieseň pridáš/odoberieš z obľúbených hviezdičkou (☆ / ★) pri každej položke v zozname. Funguje to aj z klávesnice — pozri tabuľku skratiek vyššie (`*` prepne obľúbenosť označenej piesne alebo položky vo fronte). Obľúbené sa ukladajú na serveri (`data/favorites.json`) a synchronizujú sa naživo medzi všetkými otvorenými zariadeniami. Ak sa obľúbená pieseň neskôr zmaže, potichu zmizne aj zo záložky; ak sa jej zbierka premenuje, názov zbierky sa automaticky aktualizuje.

### Zmazanie a premenovanie zbierky

Pri každej záložke zbierky v administrácii sú teraz dve malé tlačidlá: **„✎"** na premenovanie (napíš nový názov a potvrď Enterom) a **„✕"** na úplné zmazanie zbierky vrátane všetkých jej piesní (appka sa pred zmazaním spýta na potvrdenie, akcia sa nedá vrátiť späť). Zmazanie/premenovanie sa premietne aj do fronty na omšu a do obľúbených piesní, ak niektorá pieseň z tejto zbierky bola pridaná tam.

| Predvoľba | Vzhľad |
|---|---|
| Slávnostná | biele pozadie, zlatý akcent |
| Cezročné obdobie | tmavozelené pozadie |
| Pôst a Advent | tmavofialové pozadie |
| Klasická | tmavomodré pozadie, zlatý akcent |
| Mučeníci a Turíce | tmavočervené pozadie |
| Jednoduchá | biele pozadie, čierny text |
| Jednoduchá invertovaná | čierne pozadie, biely text |
| Glass — svetlý | jemný pastelový farebný prechod, priesvitná zamlžená (glass) karta |
| Glass — tmavý | tmavý modro-fialový prechod, priesvitná zamlžená karta |
| Glass — oranžová | oranžový farebný prechod, priesvitná zamlžená karta |
| Glass — modrá | modrý farebný prechod, priesvitná zamlžená karta |
| Glass — fialová | fialový farebný prechod, priesvitná zamlžená karta |
| Glass — tyrkysová | tyrkysový farebný prechod, priesvitná zamlžená karta |

„Glass" predvoľby majú namiesto plnofarebného pozadia farebný prechod, na ktorom pláva priesvitná zamlžená (rozostrené sklo / *glassmorphism*) karta s textom. Tento efekt sa rovnako prejaví aj na paneloch v ovládači, administrácii a úvodnej stránke.

Zmena sa prejaví okamžite, bez obnovenia stránky, a to nielen na TV – rovnaký farebný motív sa naživo aplikuje aj na `/home`, `/tablet` a `/admin`, takže celá appka pôsobí jednotne. Zvolená predvoľba sa ukladá do `data/theme.json`, takže prežije aj reštart servera.

#### Automatický výber motívu podľa dátumu

Nad zoznamom predvolieb je zaškrtávacie políčko **„Automatický výber podľa dátumu"**. Keď je zapnuté, appka si sama vyberie farebný motív podľa dnešného dátumu — presne rovnakým princípom ako pri obrázkoch čakacej obrazovky: pri každej predvoľbe vieš nastaviť pole **„Obdobie od–do"** (formát deň.mesiac, napr. `24.12`–`6.1`; smie prechádzať cez Nový rok). V danom období sa táto predvoľba automaticky zobrazí na TV bez toho, aby si musel čokoľvek ručne prekliknúť — napríklad fialová počas Pôstu a Adventu, zelená mimo týchto období. Ak žiadne nastavené obdobie nesedí na dnešný dátum, použije sa predvolene „Cezročné obdobie" (zelená). Appka kontroluje dátum priebežne (každých pár minút), takže zmena sa prejaví aj bez reštartu, napríklad presne o polnoci pri prechode medzi obdobiami.

Kliknutím na konkrétnu predvoľbu ju zvolíš ručne — tým sa automatický režim vypne (ručná voľba má prednosť), kým ho znova nezapneš. Nastavenia období aj zapnutie/vypnutie automatiky sa ukladajú do `data/theme.json`.

### História hraných piesní

Úplne dole na stránke je karta **„História hraných piesní"** – zoznam toho, čo a kedy sa zobrazovalo na TV (dátum, čas, zbierka, číslo a názov), najnovšie hore. Zapisuje sa automaticky pri každom zobrazení piesne (z klávesnice aj z fronty), uchováva sa posledných 300 záznamov. Tlačidlom **„Vymazať históriu"** ju vynuluješ (appka sa pred tým ešte spýta na potvrdenie). Ukladá sa do `data/history.json`.

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

V administrácii je vedľa záložiek zbierok (JKS, Žalmy, Detský spevník) tlačidlo **„+"** – klikneš naň, napíšeš názov novej zbierky (napr. „Mládežnícky spevník") a potvrdíš klávesou Enter. Zbierka sa hneď objaví ako nová záložka, prázdna, pripravená na pridávanie piesní (jednotlivo aj hromadne). Netreba nič ručne upravovať ani reštartovať server.

Appka si sama vytvorí súbor `data/<nazov>.json` a uloží zoznam všetkých zbierok do `data/collections.json`.

## Záloha dát

V hlavičke administrácie je odkaz **„⬇ Stiahnuť zálohu"**, ktorý stiahne jeden JSON súbor so všetkými piesňami zo všetkých zbierok, aktuálnou frontou, zvoleným motívom (vrátane automatického režimu a nastavených období), obľúbenými piesňami a zoznamom obrázkov čakacej obrazovky (názvy). Samotné obrázkové súbory v zálohe nie sú — tie sa zálohujú zvlášť skopírovaním priečinka `public/idle-images/`.

Vedľa neho je tlačidlo **„⬆ Nahrať zálohu"** – vyberieš predtým stiahnutý súbor a po potvrdení (appka sa pred prepísaním ešte spýta, či to naozaj chceš) sa ním **prepíšu všetky piesne, fronta, zvolený motív aj obľúbené piesne**. Obrázky čakacej obrazovky sa obnovia len tie, ktorých súbor reálne stále existuje v `public/idle-images/` — ak si medzičasom priečinok s obrázkami zmazal, tie sa neobjavia (chýbal by im samotný súbor). Táto akcia sa nedá vrátiť späť, appka na to pri nahrávaní upozorní.

## .gitignore

Repozitár obsahuje `.gitignore`, ktorý vynecháva len súbory, čo si appka sama vytvára/prepisuje za behu (`data/queue.json`, `data/theme.json`, `data/history.json`, `data/auth.json`, `data/collections.json`, `data/favorites.json`, `data/idle-images.json`, a nahraté obrázky v `public/idle-images/`). Skutočné zbierky piesní (`data/jks.json`, `data/zalmy.json`, `data/detsky.json` a prípadné ďalšie, ktoré si ručne pripravíš alebo chceš mať v repozitári natrvalo) ignorované nie sú — commituj ich normálne. Pozor: keďže `data/collections.json` je v `.gitignore`, zbierky pridané cez tlačidlo „+" v administrácii sa pri čerstvom klonovaní repozitára neobjavia, kým ich znova nepridáš (alebo kým si `data/collections.json` necommitneš zámerne, mimo `.gitignore`).

## Vyhľadávanie bez diakritiky

Vyhľadávanie piesní (na ovládači aj v administrácii) a vyhľadávanie obrázkov čakacej obrazovky funguje **bez ohľadu na diakritiku** — napr. „pastier" nájde aj „Pastier". Na ovládači navyše vyhľadávanie piesní prehľadáva **všetky zbierky naraz**, nielen práve otvorenú záložku.

## Ako to funguje (prehľad súborov)

- `server.js` – server bez akýchkoľvek npm balíčkov: stránky, API pre piesne (aj hromadný import), zbierky (aj premenovanie a zmazanie), frontu, obľúbené piesne, motívy (aj automatický výber podľa dátumu), obrázky (aj podľa dátumu), históriu a prihlásenie do administrácie (s možnosťou zmeny hesla)
- `public/home.html` – úvodná stránka s výberom Ovládač / TV / Administrácia
- `public/tablet.html` – ovládací panel: záložky zbierok (vrátane „★ Obľúbené"), klávesnica so zadávaním čísla aj slohy (`125,3`), vyhľadávanie, fronta, navigácia slôh, ručná veľkosť textu, plné ovládanie klávesnicou/numpadom (výber šípkami v zozname aj vo fronte cez Insert, `*`/`+`/`-`)
- `public/tv.html` – zobrazenie pre TV, live aktualizácia cez server-sent events, automatické aj ručné prispôsobenie textu, farebný motív (aj automaticky podľa dátumu), obrázok čakacej obrazovky podľa dátumu
- `public/admin.html` – správa zbierok (pridanie, premenovanie, zmazanie) a piesní (aj hromadne), farebných predvolieb (aj automatický režim), obrázkov, zmena hesla a história hraných piesní – chránená heslom
- `data/*.json` – databázy piesní jednotlivých zbierok, fronta, obľúbené piesne, zvolený motív, heslo (zahashované) a história
