---
layout: page
title: Vývojářská
permalink: /vyvojarska/
---

## Vývojářská dokumentace

V této části je popsaná struktura a fungování aplikace.

Dokumentaci vygenerovanou službou Doxygen z komentářů ke zdrojovému kódu je možné zobrazit [zde](https://bliakher.github.io/uredni_desky_docs/doxygen).

### OFN pro úřední desky

Úřední desky, se kterými pracujeme jsou zveřejněné jako otevřená data podle specifikace [Otevřené normální formy pro úřední desky](https://ofn.gov.cz/úřední-desky/2021-07-20/).

### Externí systémy

#### [NKOD](https://data.gov.cz/) - Národní katalog otevřených dat 

Národní katalog otevřených dat (NKOD) je informační systém spravovaný Ministerstvem vnitra, který slouží k evidenci zveřejněných otevřených dat a umožňuje přístup k těmto datům. 

V NKOD jsou zveřejněné datové sady s úředními deskami. Datová sada v katalogu obsahuje metadata ke zveřejněným datům a odkaz na distribuci. 

Využíváme [SPARQL endpoint](https://data.gov.cz/sparql) NKOD k získání všech datových sad, které odpovídají OFN pro úřední desky.


#### [RPP](https://www.szrcr.cz/cs/registr-prav-a-povinnosti) - Registr práv a povinností 

Registr práv a povinností (RPP) je jedním ze základních registrů státní správy, který eviduje údaje o orgánech veřejné moci, jejich agendách, právech a povinnostech

Aplikace ze [SPARQL endpointu](https://rpp-opendata.egon.gov.cz/odrpp/sparql) RPP dostává informace o právní formě orgánů veřejné moci, které jsou poskytovateli dat z úředních desek, a k získání jejich adresy sídla. Adresy získává jako IRI odkazující se do RÚIAN.

#### [RÚIAN](https://www.cuzk.cz/ruian/RUIAN.aspx) -  Registr územní identifikace, adres a nemovitostí 

Registr územní identifikace, adres a nemovitostí (RÚIAN) je registr státní správy, který obsahuje informace o adresách a územních prvcích. Záznam adresního místa v RÚIAN obsahuje lidsky čitelnou adresu a souřadnice adresního bodu, které je možné využít pro vizualizaci v mapě.

Aplikace využívá [SPARQL endpoint](https://linked.cuzk.cz.opendata.cz/sparql) RÚIAN k převedení IRI adresního místa na souřadnice adresy poskytovatele. Jedná se o exporimentální endpoint provozovaný na MFF UK.

### Adresářová struktura repozitáře

Repozitář: <https://github.com/bliakher/uredni_desky>

```
├── README.md
├── aplikace
│   ├── README.md
│   ├── package-lock.json
│   ├── package.json
│   ├── node_modules
│   │   └── ...
│   ├── public
│   │   └── ...
│   ├── build
│   │   └── ...
│   ├── src
│   │   ├── App.css
│   │   ├── App.test.tsx
│   │   ├── App.tsx
│   │   ├── Utils.tsx
│   │   ├── index.css
│   │   ├── index.tsx
│   │   ├── model
│   │   │   ├── DatasetStore.ts
│   │   │   ├── InfoRecord.ts
│   │   │   ├── Provider.ts
│   │   │   ├── cancelablePromise.ts
│   │   │   └── dataset.ts
│   │   ├── pages
│   │   │   ├── BulletinController.tsx
│   │   │   ├── Home.tsx
│   │   │   ├── Layout.tsx
│   │   │   ├── List.tsx
│   │   │   ├── Map.tsx
│   │   │   ├── NoPage.tsx
│   │   │   ├── detail
│   │   │   │   ├── Detail.tsx
│   │   │   │   └── InfoCards.tsx
│   │   │   ├── statistics
│   │   │   │   ├── ProviderStatistics.tsx
│   │   │   │   ├── Statistics.tsx
│   │   │   │   └── ValidationStatistics.tsx
│   │   │   └── validation
│   │   │       ├── Validation.tsx
│   │   │       └── ValidationDetail.tsx
│   │   ├── services
│   │   │   └── query.ts
│   │   └── statni_znak.png
│   └── tsconfig.json
└── dokumentace
    └── use_cases
        └── ...

```

Repozitář je rozdělený do 2 složek - *aplikace* a *dokumentace*. Ve složce *dokumentace* jsou pouze diagramy případů užití aplikace.

Složka validace:
- `package.json`, `package-lock.json` - konfigrace pro balíčkovací správce `npm`
- `node_modules/` - složka s balíčky, které aplikace používá
- `public/` - složka se statickým obsahem - např. logo aplikace
- `build/` složka, která se vygeneruje po sestavení aplikace
- `src/` - složka, která obsahuje veškerý zdrojový kód
- `model/` - model aplikace - třídy a rozhraní představující entity, které aplikace používá
- `pages/` - složka obsahující React komponenty 
- `services/` - složka obsahující služby pro komunikace s externími systémy


### Struktura aplikace

Aplikace je navržená jako *single-page application* implementovaná na straně klienta v jazyce TypeScript s použitím frameworku React. Aplikace je nasazená na GitHub Pages.

Aplikace má 3 hlavní části.

#### Komunikační brána

První částí je komunikační brána, která se stará o komunikaci s externími službami, jako jsou SPARQL endpointy NKOD, RPP a RÚIAN, a získávání dat, se kterými aplikace pracuje. 

Zdrojový kód této části najdeme v souboru `/aplikace/src/services/query.ts`. Z tohoto souboru jsou exportované funkce, které se starají o komunikaci s endpointy. Umí na základě parametrů sestavit a odeslat SPARQL dotaz a vrátit získaná data.

#### Model

Druhou částí je model. Jedná se o soubory ve složce `/aplikace/src/model/`. Jsou zde třídy a rozhraní, které reprezentují entity ze světa otevřených dat z úředních desek.

V souboru `dataset.ts` je rozhraní `QueryResponse`, které představuje metadata k jedné datové sadě s úřední deskou, kterou dostaneme z dotazu na NKOD. 

V rámci modelu si metadata o datové sadě získaná jako `QueryResponse` reprezentujeme třídou `BulletinData` ze stejného souboru. Tato třída představuje jednu úřední desku a její datovou sadu.  

Datová sada má svoji distribuci, která obsahuje reálná data z úřední desky. Distribuci představuje třída `BulletinDistribution`. Odkaz na distribuci má v sobě třída `BulletinData`, která také umí dotazem distribuci stáhnout.

Třída `BulletinDistribution` obsahuje atributy celé úřední desky a seznam informací, které jsou na ní vyvěšené. Informaci na desce reprezentuje třída `InfoRecord` ze souboru `InfoRecord.ts`. 

Informace na desce má datum vyvěšení a datum relevance, které jsou reprezentované třídou `TimeMoment`. `TimeMoment` odpovídá specifikaci [OFN Časový okamžik](https://ofn.gov.cz/časová-specifikace/2020-07-01/#vazba-časová-specifikace-časový-okamžik), kdy čas může být neurčený.

Třída `BulletinData` si v sobě drží informace o poskytovateli dat, reprezentované třídou `Provider` ze souboru `Provider.ts`. Poskytovatel má název, IRI, právní formu a adresu sídla.

Třída `DatasetStore` ze souboru `DatasetStore.ts` představuje uložiště, které používá služby komunikační brány k získání dat z úředních desek. Třída umožňuje získat všechny datové sady, které odpovídají specifikaci [OFN pro úřední desky](https://ofn.gov.cz/úřední-desky/2021-07-20/), nebo získat jednu konkrétní datovou sadu na základě jejího IRI.


#### Prezentační vrstva a kontroler

Třetí částí je kontrolor a prezentační vrstva. Prezentační vrstva vizualizuje data
z modelu a vytváří uživatelské rozhraní. Kontrolor na základě podnětů z uživatelského rozhraní vyvolává změny v modelu, které se pak promítají do změn ve
vizualizaci prezenční vrstvy.

Uživatelské rozhraní tvoří React komponenty, které najdeme ve složce `/aplikace/src/pages/`. React komponenty využívají třídu `DatasetStore` z modelu k získání dat z úředních desek reprezentovaných třídou `BulletinData`. 

Ve složce `detail/` jsou komponenty pro vizualizaci detailu úřední desky, s informacemi, které jsou na ní vyvěšené. Složka `validation/` obsahuje všechny komponenty, které se týkají validace desek. Složka `statistics/` obsahuje statistické komponenty.

Dále představíme některé důležité komponenty v aplikaci.

##### Layout

Komponenta `Layout` (soubor `Layout.tsx`) určuje rozložení stránky, kdy v horní části obrazovky je umístěn navigační panel a další obsah, v podobě ostatních komponent se renderuje uvnitř komponenty `Layout`.

##### BulletinController

Komponenta `BulletinController` se stará o filtrování úředních desek. Komponenta má v sobě instanci třídy `DatasetStore`, která ji umožní získat data z úředních desek jako seznam objektů `BulletinData`. Komponenta obsahuje 2 formuláře, jeden pro vyhledávání v deskách a druhý pro filtrování desek podle právní formy poskytovatele. 

Komponenta na základě uživatelských vstupů z formulářů vyfiltruje ze seznamu všech `BulletinData` pouze ty odpovídající uživatelským vstupům a tento vyfiltrovaný seznam předá svojí vnitřní komponentě, která seznam vizualizuje. Formuláře pro filtrování uvnitř komponenty `BulletinController`  je tedy možné použít pro více způsobů vizualizace.

V aplikaci jsou dvě části, které využívají `BulletinController` - seznam úředních desek a tabulka s výsledky validace desek.

Aby bylo možné uchovat obsah filtrovacích formulářů i po přechodu do detailu desky nebo detailu validace, a pak návratu zpět, musí být stav formulářů z komponenty `BulletinController` být uložený o úroveň výš, tedy v komponentě `App`.

Komponenta `App` ve svém stavu uchovává nastavení filtrovacích formulářů zvlášť pro seznam desek a zvlášť pro validaci. Dovnitř těchto komponent pak hodnoty předá jako React `props` (vstupy) společně s callbacky, kterými je možné hodnoty přenastavit.

Když uživatel interaguje s formuláři v seznamu nebo validaci, zavoláním callacku se přenastaví stav komponenty `App`, což spustí render. Zároveň, o přechodu uživatele do jiného modulu a návratu zpět si aplikace pamatuje volby filtrování.

`BulletinController` slouží jako obálka s formuláři pro filtrování. To jak jsou úřední desky uvnitř formuláře zobrazené je určené tím, jaké komponenty do `BulletinController` předáme jako `props`. `BulletinController` bere na vstupu 2 komponenty - komponentu s hlavičkou a komponentu, která umí vizualizovat desky v podobě `BuletinData`.

##### List

V případě seznamu se komponentě `BulletinController` předá jako hlavička `BulletinListHeader` a pro zobrazení desek komponenta `BulletinCards`. Obě komponenty jsou ze souboru `List.tsx`.

##### Validation

Pro validační modul se komponentě `BulletinController` předá jako hlavička `ValidationHeader` a pro zobrazení desek komponenta `ValidationTable`, která zobrazuje desky jako tabulku s výsledky validace. Tyto komponenty jsou ze souboru `validation/Validation.tsx`


### React

Aplikace je implementovaná pomocí frameworku React. React vytváří uživatelské rozhraní pomocí komponent. Komponenty jsou nezávislé jednotky, které si udržují vnitřní stav a umí se vyrenderovat na základě stavu a šablony psané v syntaxi JSX. 

Základní React komponentou v aplikaci je komponenta `App` ze souboru `/aplikace/src/App.tsx`. Tato komponenta v sobě obsahuje router, který popisuje, jaká komponenta uživatelského rozhraní se má vyrenderovat v závislosti na URL, což umožňuje routování v aplikaci.

V aplikaci je pro popis routování použitá React komponenta [`HashRouter`](https://reactrouter.com/docs/en/v6/routers/hash-router), která umožňuje routovaní pomocí fragmentu URL. Tato možnost byla zvolená kvůli tomu, že nasazení pomocí služby GitHub Pages neumožňuje routování pomocí virtuální cesty, které používá komponenta [`BrowserRouter`](https://reactrouter.com/docs/en/v6/routers/browser-router).

URL stránky s detailem úřední desky nebo detailem validace vznikne tak, že se do query parametru `iri` v URL dá IRI datové sady dané desky v NKOD. Detail úřední desky má tedy stabilní URL, na které je možné přistoupit bez nutnosti proklikávat se aplikací. 

Při načtení stránky se pomocí React Hook [`useLocation`](https://reactrouter.com/docs/en/v6/hooks/use-location) získá hodnota parametru `iri` a pomocí `DatasetStore` se načtou data z dané úřední desky.


