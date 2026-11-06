# adas-export/

Export projektu `gravity-spy-predvyber` z ADAS (Datarity, instance `szylar.ambica.cloud`,
prostředí **Development**, id=2, projekt id=133).

## Jak projekt v ADAS vypadá

- **Transformace `stg_glitch_metrics`** (SQL, id=1272) — z `raw_gravity_spy_classifications`
  počítá normalizovanou Shannonovu entropii (Σ -p·ln(p) / ln(22)) nad pravděpodobnostním
  vektorem 22 tříd Gravity Spy a odstup top1 − top2. Výsledek: tabulka `stg_glitch_metrics`.
- **Transformace `mart_glitch_kandidati`** (SQL, id=1273) — prefiltr `SNR > 10` a
  `entropie_norm > 0.4`. Výsledek: tabulka `mart_glitch_kandidati`.
- **Workflow `wfl_predvyber_glitchu`** (id=200) — orchestrace stg → mart.

Vstupní tabulka `raw_gravity_spy_classifications` se očekává jako 1:1 import CSV ze
Zenoda (DOI 10.5281/zenodo.5649212, např. `H1_O1.csv`). Projekt je prázdná kostra —
CSV se do něj nenahrálo (podle zadání 3. dílu).

## Soubor exportu

Automatické stažení ZIPu z MCP serveru na lokální disk nefunguje (401 Unauthorized —
endpoint `/dwa/File/…` vyžaduje interaktivní session). ZIP (8 869 B) leží na instanci:

- **Datarity UI** — Projects → `gravity-spy-predvyber` → More options → **Files** →
  `exports/gravity-spy-predvyber-export.zip` → Download.
- Přímý odkaz pro přihlášeného: <https://szylar.ambica.cloud/dwa/File/7a4bc529-1930-4a36-b38a-9ad9a9df1fc0?environment=2&directory=Projects&entities=133>

Stažený ZIP ulož sem jako `gravity-spy-predvyber-export.zip` a připoj dalším commitem.

## Odkazy do ADAS

- Workflow: <https://szylar.ambica.cloud/workflows/200?env=2>
- stg_glitch_metrics: <https://szylar.ambica.cloud/transformations/1272?env=2>
- mart_glitch_kandidati: <https://szylar.ambica.cloud/transformations/1273?env=2>
