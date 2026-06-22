# Forecasting Product Demand Using Multivariate Time Series Models

**Master's Thesis — Berliner Hochschule für Technik (BHT Berlin)**  
**Author:** Shruti Patil 


---

## Overview

This project investigates store-level daily sales forecasting using the **Rossmann Store Sales** dataset (Kaggle). The core model I used is **SARIMAX** (Seasonal Autoregressive Integrated Moving Average with Exogenous Variables), applied through a structured four-stage methodological framework.

The analysis focuses on three key modelling decisions for an individual store:
- How does **training window length** affect forecast accuracy?
- Does the **model structure remain stable** over time?
- How does **training data length** influence performance?
- It then extends **the framework to all stores in the network** to observe how forecasting results vary at scale.

---

## Research Questions

1. What training window length provides the best forecasting performance for an individual store on a fixed test window?
2. Which model structures remain stable across time?
3. How does the amount of historical training data influence forecasting performance?
4. How do forecasting results vary across the full store network when a fixed training and test configuration is applied?

---

## Dataset

**Source:** [Rossmann Store Sales — Kaggle](https://www.kaggle.com/competitions/rossmann-store-sales/data)

| File | Description |
|------|-------------|
| `train.csv` | Daily transactional data per store (Sales, Open, Promo, etc.) |
| `store.csv` | Static store attributes (StoreType, Assortment, CompetitionDistance, etc.) |

- **Stores:** 1,115 Rossmann drugstores in Germany
- **Period:** January 2013 – July 2015
- **Observations:** 1,017,209 daily records after merging ( more than 1 Million) 

---

## Methodology — Four Stages

### Stage 1 — Training Window Selection
- **Method:** Fixed-origin expanding window search
- **Windows tested:** 4 to 104 weeks (101 windows, 1-week increments)
- **Test period:** Fixed 14-day holdout
- **Result:** Optimal training window = **81 weeks** (lowest RMSE)

### Stage 2 — Model Stability Analysis
- **Method:** Forward sliding window evaluation
- **Training size:** Fixed at ~577 days
- **Blocks:** 26 forward-sliding evaluation blocks
- **Result:** Most stable model = **SARIMAX(0,0,1)(2,0,2)[7]**

### Stage 3 — Training Data Length Analysis
- **Method:** Backward rolling evaluation blocks
- **Training lengths tested:** 1 to 12 months
- **Blocks:** 26 backward evaluation blocks
- **Result:** Optimal training length = **12 months** (lowest mean RMSE); performance plateaus after ~6 months

### Stage 4 — Network-Wide Forecasting
- **Scope:** All stores
- **Setup:** Fixed 81-week training window + fixed 14-day test window
- **Model selection:** Per-store AutoARIMA + backward elimination
- **Result:** Median RMSE = 684.62 across the network

---

## Model

**SARIMAX(p,d,q)(P,D,Q)[m]** with m = 7 (weekly seasonality)

### Key Parameters

| Parameter | Value |
|-----------|-------|
| Seasonal period (m) | 7 |
| Non-seasonal differencing (d) | 0 |
| Max p, q | 3 |
| Max P, Q | 2 |
| Order selection | AutoARIMA (AIC) |
| Variable selection | Backward elimination (p ≤ 0.05) |

### Best Model — Store 1 (Stage 1)
`SARIMAX(0,0,0)(2,0,0)[7]`

| Metric | Value |
|--------|-------|
| MAPE | 4.7% |
| RMSE | 225.35 |
| MAE | 178.85 |
| R² | 0.983 |
| AIC | 9770.6 |

### Stable Model — Store 1 (Stage 2)
`SARIMAX(0,0,1)(2,0,2)[7]`

---

## Exogenous Variables

Variables consistently retained across all models:

| Variable | Frequency (Store 1) | Frequency (All Stores) |
|----------|--------------------|-----------------------|
| Open | 100% | 100% |
| Promo | 100% | 100% |
| DOW_2 (Tuesday) | 100% | 95.2% |
| DOW_3 (Wednesday) | 100% | 97.0% |
| DOW_4 (Thursday) | 100% | 97.1% |
| DOW_5 (Friday) | 100% | 90.6% |
| DOW_6 (Saturday) | 100% | 81.9% |
| SchoolHoliday | Sporadic | 53.8% |

---

## Key Findings

- **Weekly seasonality (m=7)** with P=2 is the dominant structural feature — present in 91.7% of all stores
- **Open** and **Promo** are universal predictors across all stores (100%)
- **More training data is not always better** — performance plateaus after ~6 months
- **84.8%** of stores required a different model structure than Store 1 — confirming the need for per-store automated selection
- **4.2% of stores (47 stores)** were RMSE outliers — 91.5% of these were "Always Closed on Sunday" stores with irregular sales dynamics

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| Python | Core language |
| statsmodels | SARIMAX model estimation |
| pmdarima | AutoARIMA order selection |
| pandas / NumPy | Data manipulation |
| scikit-learn | Forecast performance metrics |
| Matplotlib / Seaborn | Visualisation |

---


## Limitations

- AutoARIMA with backward elimination is computationally expensive at scale
- Only short-term forecasting evaluated (14-day test window)
- No external variables (weather, regional events) included
- Stores modelled independently — inter-store relationships not considered
- Christmas/New Year period shows consistently higher forecasting errors

---

## Future Work

- Incorporate external variables (weather, local events)
- Explore hybrid SARIMAX + ML approaches (gradient boosting, neural networks)
- Investigate hierarchical or clustered forecasting methods
- Extend test horizon beyond 14 days

---

## Citation

If you use this work, please cite:

```
Patil, S. (2026). Forecasting Product Demand Using Multivariate Time Series Models.
Master's Thesis, Berliner Hochschule für Technik (BHT Berlin).
```

---

## License

This project is for academic purposes. Dataset belongs to Rossmann / Kaggle — please refer to the [original competition](https://www.kaggle.com/competitions/rossmann-store-sales) for data usage terms.
