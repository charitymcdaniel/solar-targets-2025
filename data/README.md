# Data notes

This folder documents the inputs and transformations used to build the Excel table feeding the Tableau dashboard.

## Sources
1) **World Bank — Access to electricity (% of population)**  
   Indicator: `EG.ELC.ACCS.ZS`  
   Link: https://data.worldbank.org/indicator/EG.ELC.ACCS.ZS  
   *Why:* shows each country’s share of population with electricity (direct measure of the access gap).

2) **Our World in Data — Energy (Solar generation/capacity)**  
   Link: https://ourworldindata.org/energy  
   *Why:* indicates where solar is present and scaling (used as the solar potential signal).

**Accessed:** 2025-MM-DD (update this date when you refresh)

## Fields used (long/tidy table)
- `Country Name` — country label (string)
- `Country Code` — ISO-3 (string; join key)
- `Year` — numeric year
- `Electricity Access` — % of population with access (0–100)
- `Solar Electricity` — solar metric (handled as %, see note below)
- `Continent` — derived from `Country Code` via lookup

> **Note on Solar:** In the viz we treat solar as a percent indicator for comparability and in the Need Score. Where OWID reports TWh/capacity only, we transformed to a 0–100 style % field for presentation/relative ranking. Countries with missing solar remain in the data (map size ≈ tiny).

## Transformations (Excel / Power Query)
1) **Scope years:** kept overlapping years only; final lens is **2022**.  
2) **Filter entities:** removed regional aggregates (continents, income groups); kept **countries** only.  
3) **Tidy fields:** trimmed spaces, fixed blanks, set numeric types.  
4) **Reshape:** unpivoted to long format: *(Country, Code, Year, Metric → values)*.  
5) **Merge:** joined Access and Solar on **Country Code + Year**.  
6) **Continent map:** created `Continent_Map` and added `Continent` via **VLOOKUP** on ISO-3.  
7) **Final filter:** retained rows with **2022 Electricity Access** (keep Solar if present).  
8) **Checks:** spot-checked duplicate keys, out-of-range values (Access 0–100), and missing codes.

## Join logic
- Key: `Country Code` (ISO-3) + `Year`
- If duplicates per key exist, we average within key for reporting (Tableau uses AVG).

## Caveats
- Countries lacking **2022 access** are excluded from the dashboard.  
- Some **2022 solar** values are missing; shown as tiny/no size on the map.  
- Access kept on a **0–100** scale across visuals for interpretability.

## Refresh guide
- Re-download both sources, re-run the same Power Query steps, and re-generate the merged long table.  
- Update the **Accessed** date above and re-publish the Tableau workbook.

## Citations (see main README for full list)
- World Bank (WDI). *Access to electricity (% of population)*, `EG.ELC.ACCS.ZS`.  
- Ritchie, H., Rosado, P., & Roser, M. (2023). *Energy*. Our World in Data.
