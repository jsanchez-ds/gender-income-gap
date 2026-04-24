🌐 [English](README.md) · **Español**

# Brecha de Ingresos por Género en el Pequeño Comercio

Cuantificación de la brecha de ingresos por género entre pequeños comerciantes en Latinoamérica, usando datos transaccionales de una plataforma de pagos digital. El análisis combina **exploración descriptiva**, **regresión de efectos fijos**, **modelos lineales regularizados** y **machine learning** (CART, MARS, KNN, Random Forest) para medir la brecha e identificar qué factores observables la explican.

[![Render Rmd](https://github.com/jsanchez-ds/gender-income-gap/actions/workflows/render.yml/badge.svg)](https://github.com/jsanchez-ds/gender-income-gap/actions/workflows/render.yml)
[![View Report](https://img.shields.io/badge/Ver_Reporte-GitHub_Pages-2ea44f?logo=github)](https://jsanchez-ds.github.io/gender-income-gap/)
![R](https://img.shields.io/badge/R-4.3-276DC3?logo=r&logoColor=white)
![fixest](https://img.shields.io/badge/fixest-0.11-blue)
![glmnet](https://img.shields.io/badge/glmnet-Ridge%20%2F%20LASSO-orange)

> **[→ Lee el reporte completo renderizado](https://jsanchez-ds.github.io/gender-income-gap/)** — cada gráfico, tabla y output del modelo, sin requerir instalación de R.

---

## Pregunta de investigación

> **¿Existe una brecha de ingresos por género entre pequeños comerciantes? Si existe, ¿qué tan grande es, qué tan dispersa está a través de categorías de negocio y zonas, y cuánto de ella puede explicarse por controles observables (horas trabajadas, tipo de negocio, transacciones, ubicación, edad)?**

La brecha de pago por género está bien documentada en el empleo formal, pero la evidencia es mucho más escasa para micro-comerciantes cuentapropistas — exactamente el segmento que captura este dataset.

---

## Dataset

El Rmd lee desde `data/sample.csv` — una muestra **completamente sintética** de 540 filas (60 comerciantes × 9 semanas) generada para:

- Respetar el schema original columna por columna
- Coincidir con las distribuciones marginales de `genero`, `rubro` y `cuarentena1`
- Reproducir aproximadamente el hallazgo principal (brecha bruta ~ 20%) y la mediación esperada vía horas trabajadas y categoría de negocio

El dataset comercial original de 245,000 filas **no se redistribuye** porque fue entregado bajo los términos de un curso académico en la Universidad de Chile. Ver [`data/README.md`](data/README.md) para el schema completo.

> La plataforma registra cada dispositivo de pago bajo un único operador responsable con dirección y género conocidos, lo que la vuelve un proxy limpio de quién efectivamente maneja el negocio.

---

## Metodología

### 1. Análisis exploratorio
- Distribuciones univariadas de ingreso por género (kernel density, overlay con promedio)
- Ingreso por rango de edad y por categoría de negocio (`rubro`)
- Vista de serie temporal de ingreso por semanas del año
- Efecto de cuarentena COVID sobre ingreso por género

### 2. Modelos lineales con controles progresivos (`fixest::feols`)
| Modelo | Spec |
|---|---|
| `m1` | `log(ingreso) ~ género` (brecha bruta) |
| `m2` | `+ horas, transacciones/hora, dummies de rubro` |
| `m3` | `+ efectos fijos de zona y rango etario` |

### 3. Selección automática de features
- **Regresión stepwise Backward / Forward** (`step`)
- **Ridge / LASSO** vía `glmnet` (α = 0 / α = 1, λ = 0.05)
- Comparación de R² ajustado, RMSE y AIC

### 4. Descomposición en canales
Re-estimación de los mismos controles sobre:
- `log(horas_trabajadas)` — ¿la brecha viene de trabajar menos?
- `log(ingreso_por_hora)` — ¿o de menor productividad horaria?

### 5. Comparación con machine learning
Split train/test + tuning basado en caret:
- **CART** (`rpart2`) — grid de profundidad del árbol
- **MARS** (`earth`) — grid de grado y #términos
- **KNN** — grid de k
- **Random Forest**
- Selección por RMSE test-set

---

## Hallazgos principales

1. **Brecha bruta ≈ 20.7%** — las mujeres ganan en promedio ~20.7% menos que los hombres por semana, con `R² ≈ 0.02` (género por sí solo explica casi nada de la varianza — hay grandes factores omitidos).
2. **Agregar horas trabajadas, transacciones/hora y categoría de negocio** eleva el poder explicativo a `R² ≈ 0.6` y achica significativamente el coeficiente de género — gran parte de la brecha bruta está mediada por horas y mix de categoría.
3. **La brecha es heterogénea entre categorías**: mayor en vestuario, menor en oficios y otros servicios.
4. **La variable de cuarentena COVID** no explica la brecha — las series de ingreso de ambos géneros se mueven en paralelo durante 2020–2021.
5. **Descomposición**: la brecha está dirigida **tanto** por menos horas trabajadas **como** por menor ingreso por hora, con el canal de productividad horaria siendo el más persistente de los dos después de aplicar controles.
6. **MARS superó a CART, KNN y stepwise OLS** en RMSE test-set mientras capturaba el efecto de género — seleccionado como modelo predictivo final.

---

## Stack técnico

`R` `fixest` `glmnet` `caret` `earth` `rpart` `randomForest` `ggplot2` `dplyr`

---

## Estructura del proyecto

```
gender-income-gap/
├── README.md
├── analysis.Rmd                    # Análisis completo (EDA + modelos + ML)
├── data/
│   ├── README.md                   # Referencia de schema
│   └── sample.csv                  # Muestra sintética de 540 filas
└── .github/workflows/
    └── render.yml                  # CI: renderiza Rmd → HTML → Pages
```

---

## Cómo reproducir

### Opción A — Lee el reporte renderizado (sin instalar nada)

Abre <https://jsanchez-ds.github.io/gender-income-gap/>. El CI reconstruye el reporte en cada push a `main`.

### Opción B — Correr localmente

```r
install.packages(c(
  "rmarkdown", "knitr", "tidyverse", "dplyr", "readr", "ggplot2",
  "ggcorrplot", "fixest", "glmnet", "caret", "earth", "rpart",
  "randomForest", "kableExtra", "modelsummary"
))

rmarkdown::render("analysis.Rmd", output_dir = "docs", output_file = "index.html")
```

El Rmd lee `data/sample.csv` del repo, así que no hace falta descargar datos.

---

## Contexto

Originalmente desarrollado para **IN5162 — Marketing Engineering**, dictado por el Prof. Marcel Goic en la **Universidad de Chile (Ingeniería Industrial, 2023)**. Reformulado y reescrito en inglés para presentación de portfolio.

---

## Autor

**Jonathan Sánchez**
- GitHub: [@jsanchez-ds](https://github.com/jsanchez-ds)
- Universidad de Chile — Ingeniería Civil Industrial
