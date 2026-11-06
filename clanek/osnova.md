# 3. díl — Předvýběr kandidátů vlastním filtrem

**Stav:** osnova, článek zatím nenapsán.

## Pracovní název
„Jak si vybrat glitche, které stojí za pohled — předvýběr z Gravity Spy Open Dat"

## Osnova

1. **Úvod — proč předvýběr.** Gravity Spy předkládá poruchy podle fronty.
   Většina je nudná, model si je jistý. Otevřená data umožňují obrátit postup
   a sáhnout si jen pro zajímavé případy.

2. **Zdroj dat.** Zenodo DOI 10.5281/zenodo.5649212, licence CC BY 4.0.
   8 souborů CSV (O1–O3b, H1 + L1), ~742 MB. Pro ukázku `H1_O1.csv` — 16 MB, 15 305 řádků.
   Co obsahuje: identifikace, fyzikální metadata, pravděpodobnostní vektor pro 22 tříd,
   odkazy na spektrogramy.

3. **Myšlenka filtru.**
   - **Normalizovaná entropie** pravděpodobnostního vektoru = jak moc si model NENÍ jistý.
   - **Odstup top1 − top2** = jak blízko je druhá varianta.
   - **SNR > 10** = aby se neřešil jen šum.
   Prototyp: 15 305 → **122 kandidátů (0,8 %)** při entropii > 0,4 a SNR > 10.

4. **Realizace v ADAS (Datarity).** Projekt `gravity-spy-predvyber`, transformace
   počítá entropii, odstup a filtr. Export projektu je součástí repa.

5. **Kontaktní list kandidátů.** Pro každý: `gravityspy_id`, čtyři URL spektrogramů,
   hodnoty, kvůli kterým prošel.

6. **Vizuální porovnání.** Mozaika obrázků vedle toho, co o nich tvrdí model.

7. **Vstup do prostředí Gravity Spy.** Vzít nejzajímavější `gravityspy_id`,
   zadat do Similarity Search na Gravity Spy Tools — projekt sám založí
   veřejnou kolekci podobných.

8. **Co to opravdu umí a co ne.** Data jsou O1–O3b (uzavřený set).
   Pro O4 veřejné vydání zatím neexistuje. Filtr je tedy metoda a ověření
   přístupu, ne přímý přínos k dnešní práci.

9. **Zdroje.** Zenodo, Gravity Spy Tools, Glanzer 2023, GWOSC, repo na GitHubu.

## Repo
- GitHub: https://github.com/robozor/gravity-spy-predvyber
