# gravity-spy-predvyber

**Datarity (ADAS) projekt** pro předvýběr zajímavých poruch (glitches) z datasetu
[Gravity Spy ML Classifications](https://doi.org/10.5281/zenodo.5649212).

Repo hostuje strukturu a exportovaný ZIP projektu — importem do vlastní instance
Datarity (ADAS) dostanete workflow, které z CSV ze Zenoda vyrobí filtrovaný seznam
kandidátů na ruční / vizuální kontrolu.

## Cíl

Model Gravity Spy predikuje pravděpodobnost pro každou z 22 tříd poruch.
Pro ruční kontrolu drtivá většina případů není zajímavá — model si je jistý.
Projekt počítá z pravděpodobnostního vektoru dvě metriky *nejistoty modelu*
a prefiltruje jen ty, kde dává smysl se podívat:

- **normalizovaná Shannonova entropie** (Σ -p·ln(p) / ln(22), rozsah 0–1)
- **odstup top1 − top2** z pravděpodobnostního vektoru
- prahy: `SNR > 10`, `entropie_norm > 0.4`

Prototyp na `H1_O1.csv` (15 305 řádků): **122 kandidátů (0,8 %)**.

## Objekty v projektu

| Objekt | Typ | Popis | Výstupní tabulka |
|---|---|---|---|
| `stg_glitch_metrics` | SQL transformace | Z `raw_gravity_spy_classifications` spočte entropii a odstup top1−top2 | `stg_glitch_metrics` |
| `mart_glitch_kandidati` | SQL transformace | Prefiltr `SNR > 10` a `entropie_norm > 0.4`, seřadí podle entropie DESC | `mart_glitch_kandidati` |
| `wfl_predvyber_glitchu` | Workflow | Orchestrace: `stg_glitch_metrics` → `mart_glitch_kandidati` | — |

Všechna schémata jsou v SQL kvalifikována proměnnou šablony `"@{DATABASE_SCHEMA}"`
(SQL Script – PostgreSQL), takže projekt lze importovat do libovolného
PostgreSQL schématu / projektu bez úprav kódu.

## Požadované prostředí

- **Datarity (ADAS)** s PostgreSQL skladem
- Nainstalovaná šablona **`SQL Script - PostgreSQL`** (Store, v1.0.7 nebo novější)
  — šablona se do cílového projektu doinstaluje automaticky, pokud tam není
- Prostředí lze libovolné (reference je **Development** na `aa.robozor.cz`)

## Zdrojové CSV (vstupní tabulka `raw_gravity_spy_classifications`)

Projekt očekává ve svém schématu tabulku **`raw_gravity_spy_classifications`**
nahranou 1:1 ze Zenoda:

- **DOI:** [10.5281/zenodo.5649212](https://doi.org/10.5281/zenodo.5649212)
- **Licence:** CC BY 4.0
- **Soubory:** `H1_O1.csv`, `H1_O2.csv`, … `L1_O3b.csv` (8 CSV, celkem ~742 MB)
- Pro ukázku stačí `H1_O1.csv` (16 MB, 15 305 řádků, 43 sloupců):

  ```bash
  curl -L -o H1_O1.csv \
    https://zenodo.org/api/records/5649212/files/H1_O1.csv/content
  ```

Očekávané sloupce v tabulce (výběr):
- identifikace: `gravityspy_id`, `event_time`, `ifo`, `peak_time`
- fyzikální: `snr`, `peak_frequency`, `q_value`
- 22 tříd (pravděpodobnostní vektor): `"1080Lines"`, `"1400Ripples"`,
  `"Air_Compressor"`, `"Blip"`, `"Chirp"`, `"Extremely_Loud"`, `"Helix"`,
  `"Koi_Fish"`, `"Light_Modulation"`, `"Low_Frequency_Burst"`,
  `"Low_Frequency_Lines"`, `"No_Glitch"`, `"None_of_the_Above"`,
  `"Paired_Doves"`, `"Power_Line"`, `"Repeating_Blips"`, `"Scattered_Light"`,
  `"Scratchy"`, `"Tomte"`, `"Violin_Mode"`, `"Wandering_Line"`, `"Whistle"`
- rozhodnutí modelu: `ml_label`, `ml_confidence`

## Postup importu do ADAS

1. **Založte / vyberte cílový projekt** v Datarity (ADAS). V novém projektu
   doinstalujte šablonu *SQL Script – PostgreSQL* ze Store (není-li tam).
2. **Importujte ZIP** z `adas-export/gravity-spy-predvyber-export.zip`
   (Files → Import, varianta „do existujícího projektu"). Vzniknou obě
   transformace i workflow.
3. **Nahrajte CSV** ze Zenoda do tabulky `raw_gravity_spy_classifications`
   v cílovém schématu (DataSource → import CSV / `COPY FROM` v psql / Parquet).
4. **Spusťte workflow** `wfl_predvyber_glitchu`.
5. Kandidáti budou v tabulce `mart_glitch_kandidati`.

## Struktura repa

```
.
├── README.md                              — tento soubor
├── .gitignore
├── adas-export/
│   ├── README.md                          — detaily projektu + odkazy
│   └── gravity-spy-predvyber-export.zip   — ZIP k importu do ADAS (doplní se)
└── data/
    └── README.md                          — kam a jak stáhnout CSV ze Zenoda
```

## Reference

- Dataset: Glanzer et al., *Gravity Spy Machine Learning Classifications of LIGO
  Glitches*, [Zenodo 10.5281/zenodo.5649212](https://doi.org/10.5281/zenodo.5649212)
- Popis datové sady: Glanzer et al., CQG 40 (2023), [arXiv:2208.12849](https://arxiv.org/abs/2208.12849)
- Projekt Gravity Spy na Zooniverse: <https://www.zooniverse.org/projects/zooniverse/gravity-spy>
- Gravity Spy Tools (Similarity Search): <https://gravityspytools.ciera.northwestern.edu/>

## Licence

- **Kód (SQL, konfigurace, export):** MIT
- **Dataset Gravity Spy:** CC BY 4.0 (autoři dle citace výše)
