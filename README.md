# gravity-spy-predvyber

Doprovodné materiály ke 3. dílu seriálu **Open Data v astronomii**
(kutilský seriál pro iDNES / jihoc̆as).

**Téma:** Jak si z veřejných dat projektu [Gravity Spy](https://www.zooniverse.org/projects/zooniverse/gravity-spy)
vlastním filtrem vybrat těch pár set opravdu zajímavých poruch a vstoupit s nimi
zpět do prostředí projektu (Similarity Search, vlastní kolekce).

## Obsah repa

| Adresář | Co tam je |
|---|---|
| `data/` | místo pro vstupní CSV / Parquet ze Zenodo (není verzováno) |
| `adas-export/` | export projektu z ADAS (Datarity) — definice transformace |
| `clanek/` | draft článku a osnova |

## Vstupní data

Strojové klasifikace Gravity Spy, Zenodo DOI
[10.5281/zenodo.5649212](https://doi.org/10.5281/zenodo.5649212), licence **CC BY 4.0**.

Pro první verzi stačí `H1_O1.csv` (~16 MB):

```bash
curl -L -o data/H1_O1.csv https://zenodo.org/api/records/5649212/files/H1_O1.csv/content
```

## Myšlenka filtru

Normalizovaná entropie pravděpodobnostního vektoru (22 tříd) + odstup top1 − top2 + SNR > 10.
Prototyp na `H1_O1`: **15 305 → 122 kandidátů (0,8 %)** při entropii > 0,4 a SNR > 10.

## Navazující díly

- 1. díl — *Úvod: otevřená data v astronomii*
- 2. díl — *Gravitační vlny shánějí pomocníky*
- **3. díl — *Předvýběr kandidátů vlastním filtrem*** (tento repozitář)
- 4. díl — *Měřený pokus s vlastní kolekcí* (chystá se)

## Odkazy

- Dataset na Zenodo: https://doi.org/10.5281/zenodo.5649212
- Gravity Spy Tools: https://gravityspytools.ciera.northwestern.edu/
- Gravity Spy na Zooniverse: https://www.zooniverse.org/projects/zooniverse/gravity-spy
- Glanzer et al., CQG 40 (2023): https://arxiv.org/abs/2208.12849
- GWOSC: https://gwosc.org/data/

## Licence

Texty článku a doprovodné materiály: CC BY 4.0.
Kód (např. v `adas-export/`): MIT.
