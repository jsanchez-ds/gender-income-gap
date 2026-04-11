# Data

The analysis uses `mersample.csv`, an aggregated weekly dataset of ~245,000 merchant-week observations from a Latin American digital payments platform.

The original dataset is **not redistributed** in this repository because it was provided under the terms of an academic course at Universidad de Chile (IN5162 — Marketing Engineering) and contains commercial data from a private platform.

If you want to reproduce the analysis with your own data, you'll need a CSV with at least the following columns:

| Column | Type | Description |
|---|---|---|
| `idm` | int | Merchant id |
| `genero` | int (0/1) | 1 = woman, 0 = man |
| `edad` | int | Merchant age |
| `zona` | string | Zone identifier |
| `rubro` | string | Business category |
| `semana` | int | Week of the year |
| `Año` | int | Year |
| `ingreso` | float | Weekly income |
| `dias_trabajados` | int | Days worked in the week |
| `hrs_trabajadas` | float | Hours worked in the week |
| `transacciones` | int | Number of transactions |
| `cuarentena1` | int (0/1) | COVID lockdown flag |
| `antiguedad` | float | Years on the platform |
