---
layout: page
title: Vývojářská
permalink: /vyvojarska/
---

## Vývojářská dokumentace


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

První částí je komunikační brána, která se stará o komunikaci s externími službami, jako jsou SPARQL endpointy NKOD, RPP a RÚIAN, a získávání dat, se kterými aplikace pracuje. 

Zdrojový kód této části najdeme v souboru `/aplikace/src/services/query.ts`. Z tohoto souboru jsou exportované funkce, které se starají starají o komunikaci s endpointy. Umí na základě parametrů sestavit a odeslat SPARQL dotaz a vrátit získaná data.

Druhou částí je model. Jedná se o soubory ve složce `/aplikace/src/model/`. Jsou zde třídy a rozhraní, které reprezentují entity ze světa otevřených dat z úředních desek.

