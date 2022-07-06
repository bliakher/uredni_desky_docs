---
layout: page
title: Administrátorská
permalink: /administratorska/
---

## Administrátorská dokumentace

Tato část obsahuje instrukce pro sestavení a nasazení aplikace.

Repozitář projektu <https://github.com/bliakher/uredni_desky> obsahuje 2
složky --- *aplikace* a *dokumentace*. Veškerý zdrojový kód aplikace je
ve složce *aplikace*.

K sestavení a nasazení je použit správce balíčků [npm](https://www.npmjs.com/). Soubor
*package.json* obsahuje konfiguraci balíčků, se kterými aplikace
pracuje, a nastavení příkazů pro sestavení a nasazení aplikace.

Sestavení {#sestavení .unnumbered}
---------

Sestavení aplikace v produkční verzi se provede příkazem

npm run build

Výsledek se vygeneruje do složky *build*.

Nasazení {#nasazení .unnumbered}
--------

Pro nasazení používáme službu [GitHub Pages](https://docs.github.com/en/pages/getting-started-with-github-pages/about-github-pages) a balíček [gh-pages](https://www.npmjs.com/package/gh-pages).
Nasazení aplikace je možné provést příkazem

npm run deploy

Příkaz spustí sestavení aplikace, výsledek se odešle do větve
*gh-pages*, která je nastavená jako výchozí větev pro GitHub Pages.
Služba při aktualizaci větve publikuje novou verzi aplikace.

URL aplikace po nasazení vzniká kombinací uživatelského jména na
platformě GitHub a názvu repozitáře, v našem případě
<https://bliakher.github.io/uredni_desky>. V konfiguraci *package.json*
je URL nastaveno jako domovská stránka aplikace.

Toto nastavení využívá při sestavení prostředí Create React App (viz ),
které nastaví dané URL jako kořenové URL aplikace, což je potřeba pro
správné fungování routování v aplikaci.

