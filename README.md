# CNV Analýza Exomy

Interaktivní nástroj v R Shiny pro rychlý screening a kontrolu CNV odchylek (Copy Number Variations) z exomového sekvenování. Umožňuje:

- nahrání více souborů *.coveragefin.txt* (1 soubor = 1 vzorek),
- přiřazení pohlaví (M/Ž) ke každému vzorku,
- tři přehledy: **Coverage Mean ALL**, **CNV Muži Mean**, **CNV Ženy Mean**,
- downloady do CSV,
- OMIM anotace podezřelých regionů,
  
> Limit pro upload: **30 MB** (`options(shiny.maxRequestSize = 30 * 1024^2)`).

---

## Struktura projektu (doporučení)

  - **app/** – Shiny aplikace *(app.R / helpers.R; UI+server v jednom souboru)*
  - **reference/** – OMIM referenční soubory
  - **CNV_exom.desktop/** – spouštěč (Linux)
  - **launch.sh/** – spouštěcí skript (Linux)
  - **launch_app.bat/** – spouštěcí skript (Windows)
  - **icon_exom2.png/** – ikona
  - **icon_exom2.ico/** – ikona (Windows)

---

## Požadavky
- **R ≥ 4.1**
- Balíčky: `shiny`, `bslib`, `DT`, `magrittr`  
  Instalace: `install.packages(c("shiny","bslib","DT","magrittr"))`

---

## Formát vstupu
Tabulátorově oddělené soubory **.coveragefin.txt** se sloupci:
1. `chr`
2. `start`
3. `stop`
4. `name` (gen/region)
5. `COV-mean` (průměrné pokrytí)
6. `COV-procento` (procentuální pokrytí)

> Název vzorku se bere z názvu souboru bez `.coveragefin.txt`.
> Interní pomocná proměnná **`Row_id`** slouží k dohledání původního řádku po transformacích.

---

## Postup práce (UI)
1. **Nahrát** jeden či více souborů `.coveragefin.txt`.
2. V **„Kód várky“** se automaticky ukáže kód(y) podle názvů.
3. U každého vzorku **zvolit pohlaví** (M/Ž).
4. Kliknout **Zpracovat**.  
   - Aplikace spojí pozice (1.–4. sloupec) s průměrným pokrytím (5. sloupec) a vyrobí tabulku **Coverage Mean ALL**.
   - Normalizuje pokrytí zvlášť pro **muže** a **ženy**; zobrazí jen řádky s odchylkou **|value| > 0,25**.
   - Výsledek **CNV Muži Mean** / **CNV Ženy Mean** anotuje z **OMIM**.
5. **Stahování** CSV je v levém panelu.

---

## Výstupy (ke stažení přes UI)
- **coveragemeanALL.csv** – pozice + průměrné pokrytí všech vzorků.  
- **CNV_M_mean.csv** – normalizované hodnoty pro muže (jen |value|>0,25; vč. OMIM).  
- **CNV_Z_mean.csv** – totéž pro ženy.  
- *(volitelné, aktuálně skryté v UI)*: procentové taby a jejich exporty.

---

## OMIM reference
- Umístění v projektu: `reference/` (název souboru je volný).  
- Načítá se funkcí `load_omim_file()`; anotace probíhá přes `annotate_with_omim()`.

---

## Spuštění
- **Windows:** zástupce na `app.R` (nebo vlastní `launch_app.bat`).  
- **Linux:** `R -e "shiny::runApp('app')"` nebo vlastní `launch.sh`.  
- Přímo v RStudio: `shiny::runApp()` v adresáři aplikace.

---

## Časté potíže a tipy
- **Prázdné CNV taby:** nikde nepadla odchylka **±0,25** → zkuste kontrolní vzorek.  
- **Chybí OMIM anotace:** zkontrolujte cestu/oddělovač/kódování souboru v `reference/`.  
- **Velikost nahrávání:** respektujte limit 30 MB na dávku.  
- **Názvy sloupců a pořadí:** skript očekává přesně 6 sloupců výše.
