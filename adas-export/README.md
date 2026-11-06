# adas-export/

Export projektu `gravity-spy-predvyber` z ADAS (Datarity).

- **Instance:** `aa.robozor.cz` (astronomický konektor Datarity)
- **Prostředí:** Development (id=2)
- **Projekt:** `gravity-spy-predvyber` (id=10)

## Jak projekt v ADAS vypadá

- **Transformace `stg_glitch_metrics`** (SQL, id=14) — z `raw_gravity_spy_classifications`
  počítá normalizovanou Shannonovu entropii (Σ -p·ln(p) / ln(22)) nad pravděpodobnostním
  vektorem 22 tříd Gravity Spy a odstup top1 − top2. Výstup: tabulka `stg_glitch_metrics`.
- **Transformace `mart_glitch_kandidati`** (SQL, id=15) — prefiltr `SNR > 10` a
  `entropie_norm > 0.4`. Výstup: tabulka `mart_glitch_kandidati`.
- **Workflow `wfl_predvyber_glitchu`** (id=3) — orchestrace stg → mart.

Vstupní tabulka `raw_gravity_spy_classifications` se očekává jako 1:1 import CSV ze
Zenoda (DOI 10.5281/zenodo.5649212, např. `H1_O1.csv`). Projekt je prázdná kostra —
CSV se do něj nenahrálo (podle zadání 3. dílu).

Schéma v SQL je kvalifikováno proměnnou šablony `"@{DATABASE_SCHEMA}"`, takže projekt
jde přenést do jiného prostředí bez úprav.

## Soubor exportu (ZIP)

Automatické stažení ZIPu z MCP serveru na lokální disk nefunguje (401 Unauthorized —
endpoint `/dwa/File/…` vyžaduje interaktivní session). ZIP (8 882 B) leží na instanci:

- **Datarity UI:** Projects → `gravity-spy-predvyber` → More options → **Files** →
  `exports/gravity-spy-predvyber-export.zip` → Download.
- Přímý odkaz pro přihlášeného uživatele: <https://aa.robozor.cz/dwa/File/bccb3510-e3ac-45c6-af88-39fc56f8f8e9?environment=2&directory=Projects&entities=10>

Stažený ZIP ulož sem jako `gravity-spy-predvyber-export.zip` a připoj dalším commitem.

## Odkazy do ADAS

- Workflow: <https://aa.robozor.cz/workflows/3?env=2>
- stg_glitch_metrics: <https://aa.robozor.cz/transformations/14?env=2>
- mart_glitch_kandidati: <https://aa.robozor.cz/transformations/15?env=2>
