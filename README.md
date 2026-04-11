# 💼 Gender Income Gap in Small Commerce

Quantifying the gender income gap among small merchants in Latin America using transactional data from a digital payments platform. The analysis combines **descriptive exploration**, **fixed-effects regression**, **regularized linear models**, and **machine learning** (CART, MARS, KNN, Random Forest) to measure the gap and identify which observable factors explain it.

![R](https://img.shields.io/badge/R-4.3-276DC3?logo=r&logoColor=white)
![fixest](https://img.shields.io/badge/fixest-0.11-blue)
![glmnet](https://img.shields.io/badge/glmnet-Ridge%20%2F%20LASSO-orange)
![Status](https://img.shields.io/badge/Status-Completed-green)

---

## 🎯 Research Question

> **Is there a gender income gap among small merchants? If so, how large is it, how dispersed is it across business categories and zones, and how much of it can be explained by observable controls (hours worked, business type, transactions, location, age)?**

The gender pay gap is well documented in formal employment, but evidence is much thinner for self-employed micro-merchants — exactly the segment this dataset captures.

---

## 📊 Dataset

| Feature | Detail |
|---|---|
| **Source** | Digital payments platform serving ~5,000 small merchants in a Latin American region |
| **Records** | ~245,000 weekly merchant observations |
| **Unit** | Merchant × week |
| **Target** | `log(ingreso)` — weekly income |
| **Treatment** | `genero` (1 = woman, 0 = man) |

**Key variables:** `genero`, `edad`, `zona`, `rubro` (business category), `ingreso`, `dias_trabajados`, `hrs_trabajadas`, `transacciones`, `antiguedad`, `cuarentena1` (COVID lockdown flag), `transacciones_hr`, `ingreso_hr`.

> The platform registers each payment device under a single responsible operator with known address and gender, which makes it a clean proxy for who actually runs the business.

---

## 🔬 Methodology

### 1. Exploratory Analysis
- Univariate distributions of income by gender (kernel density, mean overlay)
- Income by age range and by business category (`rubro`)
- Time-series view of income across weeks of the year
- COVID lockdown effect on income by gender

### 2. Linear Models with Increasing Controls (`fixest::feols`)
| Model | Spec |
|---|---|
| `m1` | `log(income) ~ gender` (raw gap) |
| `m2` | `+ hours, transactions/hr, business category dummies` |
| `m3` | `+ zone and age-bracket fixed effects` |

### 3. Automatic Feature Selection
- **Backward / Forward stepwise regression** (`step`)
- **Ridge / LASSO** via `glmnet` (α = 0 / α = 1, λ = 0.05)
- Compared adjusted R², RMSE and AIC

### 4. Decomposition into Channels
Re-estimating the same controls on:
- `log(hours_worked)` — does the gap come from working less?
- `log(income_per_hour)` — or from lower hourly productivity?

### 5. Machine Learning Comparison
Train/test split + caret-based tuning:
- **CART** (`rpart2`) — tree depth grid
- **MARS** (`earth`) — degree and #terms grid
- **KNN** — k grid
- **Random Forest**
- Selected on test-set RMSE

---

## 📈 Key Findings

1. **Raw gap ≈ 20.7%** — women earn on average ~20.7% less than men per week, with `R² ≈ 0.02` (gender alone explains almost nothing of the variance — there are large omitted factors).
2. **Adding hours worked, transactions/hr and business category** raises explanatory power to `R² ≈ 0.6` and shrinks the gender coefficient meaningfully — much of the raw gap is mediated by hours and category mix.
3. **The gap is heterogeneous across categories**: largest in apparel (`vestuario`), smallest in services / trades (`oficios y otros servicios`).
4. **The COVID lockdown variable** does not explain the gap — both genders' income series move in parallel through 2020–2021.
5. **Decomposition**: the gap is driven by **both** fewer hours worked **and** lower income per hour, with the hourly-productivity channel being the more persistent of the two after controls.
6. **MARS outperformed CART, KNN and stepwise OLS** on test-set RMSE while still capturing the gender effect — selected as the final predictive model.

---

## 🛠️ Tech Stack

`R` `fixest` `glmnet` `caret` `earth` `rpart` `randomForest` `ggplot2` `dplyr`

---

## 🗂️ Project Structure

```
gender-income-gap/
├── README.md
├── analysis.Rmd          # Full analysis (EDA + models + ML)
└── data/
    └── README.md         # Instructions to obtain the dataset
```

---

## 🚀 How to Reproduce

```r
# Install dependencies
install.packages(c("fixest", "glmnet", "caret", "earth",
                   "rpart", "randomForest", "ggplot2", "dplyr"))

# Open and knit the analysis
rmarkdown::render("analysis.Rmd")
```

The dataset (`mersample.csv`) is not redistributed in this repo. See `data/README.md`.

---

## 📚 Context

This analysis was originally developed for **IN5162 — Marketing Engineering**, taught by Prof. Marcel Goic at the **Universidad de Chile (Industrial Engineering, 2023)**. It has been reframed and rewritten in English for portfolio presentation.

---

## 👤 Author

**Jonathan Sánchez**
- GitHub: [@jsanchez-ds](https://github.com/jsanchez-ds)
- Universidad de Chile — Industrial Engineering
