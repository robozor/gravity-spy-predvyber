# adas-export/

Export projektu z Datarity (ADAS) — ZIP vytvořený pomocí `datarity_export_project`.
Obsahuje definici transformace, která z CSV se strojovými klasifikacemi Gravity Spy
počítá normalizovanou entropii pravděpodobnostního vektoru 22 tříd, odstup top1/top2
a filtruje záznamy se SNR > 10.
