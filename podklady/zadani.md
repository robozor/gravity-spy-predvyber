# 3. díl — Předvýběr kandidátů vlastním filtrem

**Založeno:** 30. srpna 2026
**Stav:** zadání a prerekvizity, článek zatím nenapsán
**Navazuje na:** 2. díl „Gravitační vlny shánějí pomocníky"

---

## 1. Úhel článku

Gravity Spy vám při klasifikaci předkládá poruchy tak, jak přijdou — jednu po druhé, podle
jejich fronty. Pro člověka, který má chuť hledat něco konkrétního, je to plýtvání časem: drtivá
většina případů je nudná a model si je jimi naprosto jistý.

**Otázka článku:** dá se to obrátit? Můžu si stáhnout data, vlastním filtrem si vybrat těch pár set
opravdu zajímavých případů — a pak s nimi pracovat přímo v prostředí projektu, místo abych
procházel, co mi nabídnou?

**Odpověď je ano.** A článek to ukáže krok za krokem.

Pointa pro čtenáře: otevřená data neznamenají jen „vědci něco zveřejnili". Znamenají, že si
v jejich vlastním projektu můžete určit, na co se budete dívat.

---

## 2. Ověření hlavní překážky (uděláno 30. 8. 2026)

Než má smysl článek psát, bylo nutné ověřit, že se do prostředí projektu dá vstoupit s vlastním
výběrem. Výsledek:

### 2.1 Co NEJDE

**V hlavním klasifikačním rozhraní Gravity Spy si konkrétní případ vybrat nelze.**
Ověřeno čtením zdrojového kódu rozhraní Zooniverse (`zooniverse/Panoptes-Front-End`,
soubor `app/pages/project/classify.jsx`): stránka rozumí parametrům `?workflow=`, `?group=`,
`?classroom=` a `?language=`, ale **žádnému `?subject=`**. Odkaz na konkrétní porucha ke
klasifikaci tedy neexistuje.

### 2.2 Co JDE — a je to lepší

Projekt provozuje sesterský web **Gravity Spy Tools**
(https://gravityspytools.ciera.northwestern.edu/, běží, kód na
https://github.com/Gravity-Spy/gravityspytools, poslední úprava 3. 8. 2026). Nabízí čtyři nástroje:

| Nástroj | Co dělá |
|---|---|
| **Similarity Search** | zadáte identifikátor poruchy a najde jí nejpodobnější |
| Display Your Collection With Metadata | vypíše metadata vaší kolekce |
| **Create a Workflow to Vet a Collection** | z vaší kolekce udělá klasifikační úlohu |
| Create a New ConvNN from Collection | natrénuje z kolekce vlastní síť |

**Similarity Search — jak přesně funguje** (zjištěno ze zdrojového kódu `search/utils.py`):

- Vstup: **`gravityspy_id`** (přesně ten, který je ve stažené tabulce), nebo číselné Zooniverse ID,
  nebo **GPS čas události**. Plus filtr na detektor a pozorovací běh.
- Model si o každé poruše pamatuje **vektor o 200 rozměrech** — vnitřní reprezentaci z předposlední
  vrstvy neuronové sítě. Ta je uložená v databázi PostgreSQL.
- Hledání je nejbližší soused v tomto prostoru: eukleidovská vzdálenost, `ORDER BY ... <-> cube ASC LIMIT n`.
- **Výstup: automaticky založená veřejná kolekce na Zooniverse** s nalezenými poruchami,
  vaše zadaná porucha jako titulní. Dostanete odkaz tvaru
  `https://www.zooniverse.org/projects/zooniverse/gravity-spy/collections/<slug>`.

To znamená: **hledáte podobnost v tom, co vidí sám model** — ne v metadatech, ne v obrázku.
To je pro článek silná pointa.

### 2.3 Důkaz, že to lidé skutečně používají

Dotaz na veřejné rozhraní Zooniverse (`/api/collections?project_id=1104`) vrátil
**8 740 veřejných kolekcí**. Namátkou:

- `Collection Similar to 25677153 ID xxwES` — přesně tvar názvu, který generuje Similarity Search
- `MF ripple-comb 25-150 Hz, q_value = 45.3, ER15 & O4` — ručně sestavená sbírka
- `Chirp candidates` (31 poruch), `Noisebands`, `Grav-waves`

Uživatelé si tedy vlastní sbírky konkrétních poruch skutečně staví. Není to teoretická možnost.

### 2.4 Projekt žije na aktuálních datech

Dotaz na frontu úloh (`/api/subjects/queued?workflow_id=7767`) vrátil poruchu s datem `20240512`,
tedy z běhu O4b. Metadata každého předmětu na Zooniverse obsahují:

```json
{"date":"20240512", "q_value":"45.25...", "subject_id":"n42kdmRULM",
 "#ml_top_label":"1400Ripples", "#ml_top_confidence":"0.31386375",
 "Filename1":"H1_n42kdmRULM_spectrogram_0.5.png", ...}
```

**Důležité:** pole `subject_id` v metadatech Zooniverse je totéž co `gravityspy_id` ve stažené
tabulce. Propojení mezi staženými daty a webem projektu tedy existuje a je přímé.

Diskuse (Talk) u jednotlivých poruch je aktivní a komentáře odkazují na O4b.

Statistika projektu k 30. 8. 2026: **1 404 756 předmětů, 5 825 490 klasifikací, 9 aktivních
pracovních postupů.**

### 2.5 Co zůstalo neověřeno

- **Nástroj „Create a Workflow to Vet a Collection" míří na projekt, který není veřejně dostupný.**
  Kód zakládá pracovní postup v projektu číslo 6040 a přesměruje na
  `https://www.zooniverse.org/projects/sbc538/vet-new-classes/` — ta adresa vrací **404** a projekt
  není v rozhraní k nalezení. Buď je soukromý, nebo nástroj zestárl.
  → **Ověřit po přihlášení.** Pokud nefunguje, článek postaví na Similarity Search a kolekcích,
  což samo o sobě stačí.
- Všechny nástroje vyžadují **přihlášení účtem Zooniverse** (OAuth, rozsah `collection public`).
  Bez účtu se dovnitř nepodíváme.

---

## 3. Prerekvizity

### 3.1 Účty a přístupy

| Co | Kde | Poznámka |
|---|---|---|
| Účet Zooniverse | https://www.zooniverse.org/accounts/registration | zdarma, nutný pro kolekce i nástroje |
| Nic dalšího | — | data ze Zenodu jsou bez registrace |

### 3.2 Software

- **Python 3.10+** (na pracovním stroji je 3.14.3).
- Balíčky: `pandas`, `pyarrow`, `numpy`, `matplotlib`, `requests`, `duckdb`.
  **Chybí a je nutné doinstalovat:** `pyarrow`, `matplotlib`, `duckdb`.
- Volitelně `panoptes-client` — oficiální klient Zooniverse, umí zakládat a plnit kolekce
  programově (`Collection().add([...])`). Tím by šlo obejít ruční klikání.
- **R není na stroji nainstalované.** Pokud chceme ukázku v R nad stejným Parquetem, musí se
  doinstalovat R a balíček `arrow`.

### 3.3 Vstupní data

**Hlavní zdroj — strojové klasifikace, Zenodo DOI `10.5281/zenodo.5649212`**

- Licence **CC BY 4.0** (plně otevřená, včetně odvozenin — do repozitáře bez problému).
- 8 souborů CSV, celkem **742 MB**: `H1_O1`, `H1_O2`, `H1_O3a`, `H1_O3b`, `L1_O1`, `L1_O2`,
  `L1_O3a`, `L1_O3b`.
- Pro první verzi stačí `H1_O1.csv` — **16 MB, 15 305 řádků, 43 sloupců** (staženo a ověřeno).
- Stažení bez registrace:
  `https://zenodo.org/api/records/5649212/files/H1_O1.csv/content`

Sloupce:

| Skupina | Sloupce |
|---|---|
| Identifikace | `event_time`, `ifo`, `peak_time`, `start_time`, `gravityspy_id`, `channel` |
| Fyzikální metadata | `duration`, `peak_frequency`, `central_freq`, `bandwidth`, `amplitude`, `snr`, `q_value` |
| Výstup modelu | pravděpodobnost pro každou z 22 tříd |
| Rozhodnutí | `ml_label`, `ml_confidence` |
| Obrázky | `url1`–`url4` — přímé odkazy na spektrogramy (0,5 / 1 / 2 / 4 s) |

`url1`–`url4` míří na `panoptes-uploads.zooniverse.org`, stahují se bez omezení, PNG ~300 kB.
Jsou to **přesně ty obrázky, které vidí dobrovolník**.

**Doplňkově — surová data:** GWOSC (https://gwosc.org/data/) pro tvorbu vlastních spektrogramů
nástrojem GWpy, pokud bychom chtěli jít pod úroveň hotových obrázků.

---

## 4. Navržený postup

1. **Stažení a převod do Parquetu.** CSV → Parquet, změřit rozdíl velikosti a rychlosti čtení.
   To je zároveň příspěvek k formátové lince seriálu.
2. **Filtr.** Prototyp už funguje: normovaná entropie pravděpodobnostního vektoru + odstup první
   a druhé třídy + práh na SNR.
   Ověřený výsledek na `H1_O1`: **15 305 → 122 kandidátů (0,8 %)** při entropii nad 0,4 a SNR nad 10.
   Rozšířit o hledání odlehlých bodů ve fyzikálních parametrech.
3. **Kontaktní list.** Ke každému kandidátovi vypsat `gravityspy_id`, čtyři odkazy na obrázky
   a hodnoty, kvůli kterým prošel filtrem.
4. **Vizuální porovnání.** Stáhnout obrázky kandidátů a poskládat je do přehledové tabulky vedle
   toho, co o nich tvrdí model.
5. **Vstup do prostředí projektu.** Vzít `gravityspy_id` nejzajímavějšího kandidáta, zadat ho do
   Similarity Search a nechat si založit kolekci podobných. Popsat, co to udělalo.
6. **Vyhodnocení.** Kolik z vybraných kandidátů opravdu stálo za pohled. Poctivě i to, kolik jich
   byla slepá ulička.

---

## 5. Co musí článek přiznat

- Data pokrývají **O1–O3b**, tedy poruchy, které už roztříděné jsou. Vlastní filtr na nich je
  metoda a ověření přístupu, **ne přímý příspěvek** k dnešní práci projektu.
- **Veřejné datové vydání pro běh O4 neexistuje** — ověřeno prohledáním Zenodu (13 záznamů
  „Gravity Spy"). Nejnovější jsou strojové klasifikace z 2021 a dobrovolnické z 2024.
  Živý projekt na Zooniverse ale na datech z O4 běží.
- Skutečná pomoc projektu dnes probíhá klasifikací přímo na Zooniverse.

---

## 6. Otevřené otázky před psaním

1. Funguje „Create a Workflow to Vet a Collection"? (vyžaduje přihlášení)
2. Jde přes `panoptes-client` naplnit kolekci programově seznamem `gravityspy_id`, nebo je nutné
   znát číselné Zooniverse ID? Similarity Search bere `gravityspy_id`, takže obcházka existuje.
3. Jak velkou část souborů brát — stačí `H1_O1`, nebo celých 742 MB? Pro článek nejspíš jeden
   soubor, pro repozitář volitelně vše.

---

## 7. Zdroje

- Strojové klasifikace Gravity Spy (CC BY 4.0) — https://doi.org/10.5281/zenodo.5649212
- Gravity Spy Tools — https://gravityspytools.ciera.northwestern.edu/
- Zdrojový kód nástrojů — https://github.com/Gravity-Spy/gravityspytools
- Rozhraní Zooniverse (kód klasifikační stránky) — https://github.com/zooniverse/Panoptes-Front-End
- Gravity Spy na Zooniverse — https://www.zooniverse.org/projects/zooniverse/gravity-spy
- Glanzer a kol., CQG 40 (2023), popis datové sady — https://arxiv.org/abs/2208.12849
- Hledání nových tříd poruch komunitou — https://arxiv.org/abs/2508.13923
- GWOSC (surová data) — https://gwosc.org/data/
