# Data

## Original dataset

The original analysis used `mersample.csv`, an aggregated weekly dataset of
~245,000 merchant-week observations from a Latin American digital payments
platform. **It is not redistributed in this repository** because it contains
commercial data from a private platform and was provided under the terms of
an academic course at Universidad de Chile (IN5162 — Marketing Engineering).

## Synthetic sample (`sample.csv`)

This repository ships a **fully synthetic** sample at `data/sample.csv`
(~540 rows, 60 merchants × 9 weeks). It is generated to:

- Respect the original schema column-by-column
- Match the marginal distributions of `genero`, `rubro` and `cuarentena1`
- Approximately reproduce the headline finding (raw gender gap ~ 20%) and the
  expected mediation through hours worked and business category

It is designed so that the analysis Rmd runs end-to-end against `sample.csv`
and produces qualitatively similar (but smaller-N) results. **It contains no
real records** from the original commercial dataset.

## Schema

| Column | Type | Description |
|---|---|---|
| `idm` | int | Merchant id |
| `zona` | string | Zone identifier |
| `semana` | int | Week of the year |
| `año` | int | Year |
| `semana_año` | string | Concatenated `semana-año` |
| `edad` | int | Merchant age |
| `genero` | int (0/1) | 1 = woman, 0 = man |
| `ingreso` | float | Weekly income |
| `dias_trabajados` | int | Days worked in the week |
| `antiguedad` | int | Years on the platform |
| `rubro` | string | Business category |
| `transacciones` | int | Number of transactions |
| `hrs_trabajadas` | float | Hours worked in the week |
| `cuarentena1` | int (0/1) | COVID lockdown flag |
| `hrs_diarias` | float | Average daily hours when working |
| `transacciones_hr` | float | Transactions per hour |
| `ingreso_hr` | float | Income per hour |
