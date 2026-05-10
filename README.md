# CO₂ Emissions and Fuel Consumption: A Multiple Linear Regression Analysis of Canadian Vehicle Data
 
## Overview

This project investigates how combined fuel consumption and fuel type jointly influence vehicle CO₂ emissions using multiple linear regression. Using data from 7,384 Canadian vehicles, we build a regression model that accounts for the nonlinear relationship between fuel consumption and emissions, and allows that relationship to differ across fuel types.

**Research Question:** How do combined fuel consumption and fuel type affect vehicle CO₂ emissions, and can we build a reliable model to predict them?

## Key Findings

| Result | Value |
|---|---|
| Response transformation | Square root of CO₂ (Box-Cox: λ̂ = 0.498) |
| Final model | sqrt(CO₂) ~ (fuel_comb + fuel_comb²) × fuel_type |
| Adjusted R² | 0.9974 |
| AIC | −13,992 |
| Predicted CO₂ (regular gasoline, 9.0 L/100 km) | 209.3 g/km |
| 95% Prediction Interval | (204.0, 214.7) g/km |


## Data

- **Source:** Government of Canada Fuel Consumption Ratings Database
- **Available at:** [Kaggle](https://www.kaggle.com/datasets/bhuviranga/co2-emissions)
- **Dimensions:** 7,385 rows × 12 columns (1 natural gas vehicle removed)
- **Response:** CO₂ Emissions (g/km) — range: 96 to 522
- **Predictors used:** Combined fuel consumption (L/100 km), Fuel type (X, Z, D, E)

### Fuel Type Groups

| Code | Fuel | n | Mean CO₂ (g/km) | Fuel Consumption Range |
|---|---|---|---|---|
| X | Regular Gasoline (reference) | 3,637 | 235.1 | 4.1 – 21.2 L/100 km |
| Z | Premium Gasoline | 3,202 | 266.0 | 4.5 – 22.2 L/100 km |
| E | Ethanol E85 | 370 | 275.1 | 10.1 – 26.1 L/100 km |
| D | Diesel | 175 | 237.5 | 5.5 – 12.0 L/100 km |


## Analysis Pipeline

### Step 1 — Exploratory Data Analysis
- Histogram of CO₂ shows right skew suggesting transformation needed
- Scatterplot matrix and pairwise correlations
- CO₂ vs fuel consumption by fuel type — curves differ clearly across groups

### Step 2 — Box-Cox Transformation
- Full second-order multiplicative model fitted first
- λ̂ = 0.498, 95% CI = (0.485, 0.510)
- λ = 0.5 inside CI — use sqrt(CO₂) as response
- Log (λ = 0) and no transformation (λ = 1) both rejected

### Step 3 — VIF Check
- Engine size VIF = 8.52 — excluded
- Cylinders VIF = 7.25 — excluded
- fuel_comb VIF = 3.04 — retained

### Step 4 — Model Selection (Stepwise AIC)
- Full model on sqrt(CO₂) scale fitted
- Stepwise AIC via step() with trace = 0
- All terms retained — removing any term increased AIC

### Step 5 — Final Model
- sqrt(CO₂) ~ (fuel_comb + fuel_comb²) × fuel_type
- 12 parameters, Adj R² = 0.9974, AIC = −13,992

### Step 6 — Assumption Checking
- Linearity: satisfied — no pattern in residuals vs fitted
- Constant variance: NCV test χ² = 33.2, p < 0.001 — mild heteroskedasticity
- Normality: Shapiro-Wilk W = 0.837 — violated, driven by extreme vehicles
- Outliers: 20 extreme residuals, 730 high leverage, 414 by Cook's distance

### Step 7 — Inference and Prediction
- Vehicle: regular gasoline, fuel_comb = 9.0 L/100 km (Q1 = 8.8 for X group)
- Point estimate: 209.3 g/km
- 95% Prediction Interval: (204.0, 214.7) g/km — for a single new vehicle
- 95% Confidence Interval: (209.2, 209.4) g/km — for the mean of all such vehicles

## Repository Structure

```
co2-emissions-mlr/
├── README.md                  — this file
├── project.qmd                — full step-by-step analysis code
├── co2_paper_final.qmd        — written paper (3-5 pages)
└── CO2 Emissions.csv          — dataset
```


## How to Reproduce

1. Clone this repository
```
git clone https://github.com/YOUR-USERNAME/co2-emissions-mlr.git
```

2. Open `project.qmd` in RStudio

3. Make sure the dataset `CO2 Emissions.csv` is in the same working directory

4. Install required packages
```r
install.packages("pacman")
pacman::p_load(tidyverse, MASS, car, broom)
```

5. Render the file
```
quarto render project.qmd
```


## Requirements

- R version 4.0 or higher
- Quarto
- R packages: `tidyverse`, `MASS`, `car`, `broom`, `pacman`


## References

Government of Canada. (2024). *Fuel consumption ratings open data*. Retrieved from https://open.canada.ca/data/en/dataset/98f1a129-f628-4ce4-b24d-6f16bf24cf7e

Sheather, S. J. (2009). *A modern approach to regression with R*. Springer.

Vira, B. (2024). *CO₂ emissions dataset*. Kaggle. Retrieved from https://www.kaggle.com/datasets/bhuviranga/co2-emissions
