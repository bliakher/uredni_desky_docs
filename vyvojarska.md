---
layout: page
title: Vývojářská
permalink: /vyvojarska/
---

## Vývojářská dokumentace

V této části je popsaná struktura a fungování aplikace.

Dokumentaci vygenerovanou službou Doxygen z komentářů ke zdrojovému kódu je možné zobrazit zde.

#### Externí systémy

**NKOD**: Národní katalog otevřených dat <https://data.gov.cz/>

**RPP**: Registr práv a povinností <https://www.szrcr.cz/cs/registr-prav-a-povinnosti>

**RÚIAN**: Registr územní identifikace, adres a nemovitostí <https://www.cuzk.cz/ruian/RUIAN.aspx>

#### Adresářová struktura repozitáře

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


#### Struktura aplikace

Aplikace je navržená jako *single-page application* implementovaná na straně klienta v jazyce TypeScript s použitím frameworku React.

Aplikace má 3 hlavní části.

##### Komunikační brána

První částí je komunikační brána, která se stará o komunikaci s externími službami, jako jsou SPARQL endpointy NKOD, RPP a RÚIAN, a získávání dat, se kterými aplikace pracuje. 

Zdrojový kód této části najdeme v souboru `/aplikace/src/services/query.ts`. Z tohoto souboru jsou exportované funkce, které se starají starají o komunikaci s endpointy. Umí na základě parametrů sestavit a odeslat SPARQL dotaz a vrátit získaná data.

##### Model

Druhou částí je model. Jedná se o soubory ve složce `/aplikace/src/model/`. Jsou zde třídy a rozhraní, které reprezentují entity ze světa otevřených dat z úředních desek.

V souboru `dataset.ts` je rozhraní `QueryResponse`, které představuje metadata k jedné datové sadě s úřední deskou, která dostaneme z dotazu na NKOD. 

Tato metadata si v aplikaci reprezentujeme třídou `BulletinData` ze stejného souboru, tato třída představuje jednu úřední desku a její datovou sadu.  

Datová sada má svoji distribuci, která obsahuje reálná data z úřední desky. Distribuci představuje třída `BulletinDistribution`. Odkaz na distribuci má v sobě třída `BulletinData`, která také umí dotazem distribuci stáhnout.

Třída `BulletinDistribution` obsahuje atributy celé úřední desky a seznam informací, které jsou na ní vyvěšené. Informaci na desce reprezentuje třída `InfoRecord` ze souboru `InfoRecord.ts`. 

Informace na desce má datum vyvěšení a datum relevance, které jsou reprezentované třídou `TimeMoment`. `TimeMoment` odpovídá specifikaci [OFN Časový okamžik](https://ofn.gov.cz/časová-specifikace/2020-07-01/#vazba-časová-specifikace-časový-okamžik), kdy čas může být nespecifikovaný.

Třída `BulletinData` si v sobě drží informace o poskytovateli dat, reprezentované třídou `Provider` ze souboru `Provider.ts`

Třída `DatasetStore` ze souboru `DatasetStore.ts` představuje uložiště, které používá služby komunikační brány k získání dat z úředních desek. Třída umožňuje získat všechny datové sady, které odpovídají specifikaci [OFN pro úřední desky](https://ofn.gov.cz/úřední-desky/2021-07-20/), nebo získat jednu konkrétní datovou sadu na základě jejího IRI.


##### Prezentační vrstva a kontroler

