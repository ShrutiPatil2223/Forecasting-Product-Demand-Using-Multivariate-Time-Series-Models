# Forecasting Product Demand Using Multivariate Time Series Models

**Master's Thesis — Berliner Hochschule für Technik (BHT Berlin)**
**Author:** Shruti Patil
---
## Overview

This project investigates store-level daily sales forecasting using the **Rossmann Store Sales** dataset from Kaggle. The core forecasting model is **SARIMAX** (Seasonal Autoregressive Integrated Moving Average with Exogenous Variables), combined with automated order selection and feature selection techniques.

The analysis focuses on three key modelling decisions for an individual store:

* How does **training window length** affect forecast accuracy?
* Does the **model structure remain stable** over time?
* How does **training data length** influence performance?

The framework is then extended to all stores in the network to evaluate forecasting performance at scale.
---
## Research Questions

1. What training window length provides the best forecasting performance for an individual store on a fixed test window?
2. Which model structures remain stable across time?
3. How does the amount of historical training data influence forecasting performance?
4. How do forecasting results vary across the full store network when a fixed training and test configuration is applied?
---
## Dataset

**Source:** Rossmann Store Sales (Kaggle)

| File        | Description                                                                |
| ----------- | -------------------------------------------------------------------------- |
| `train.csv` | Daily transactional data per store (Sales, Open, Promo, etc.)              |
| `store.csv` | Static store attributes (StoreType, Assortment, CompetitionDistance, etc.) |

### Dataset Characteristics

* **Stores:** 1,115 Rossmann drugstores in Germany
* **Period:** January 2013 – July 2015
* **Observations:** 1,017,209 daily records
* **Target Variable:** Daily Sales

Key variables include:

* Sales
* Open
* Promo
* Customers
* SchoolHoliday
* StateHoliday
* StoreType
* Assortment
* CompetitionDistance
* Promo2

---

## Methodology — Four Stages

### Stage 1 — Training Window Selection

* Method: Fixed-origin expanding window search
* Windows tested: 4 to 104 weeks
* Total windows evaluated: 101
* Test period: Fixed 14-day holdout
* Result: Optimal training window = **81 weeks**

### Stage 2 — Model Stability Analysis

* Method: Forward sliding window evaluation
* Training size: ~577 days
* Evaluation blocks: 26
* AutoARIMA and variable selection repeated independently for each block
* Result: Most stable model = **SARIMAX(0,0,1)(2,0,2)[7]**

### Stage 3 — Training Data Length Analysis

* Method: Backward rolling evaluation blocks
* Training lengths tested: 1–12 months
* Evaluation blocks: 26
* Result: Best performance achieved with **12 months** of training data
* Performance improvements became marginal after approximately **6 months**

### Stage 4 — Network-Wide Forecasting

* Applied to all **1,115 stores**
* Fixed training window: 81 weeks
* Fixed test period: 14 days
* AutoARIMA used for order selection
* Backward elimination used for exogenous variable selection
* Results compiled for network-wide analysis

---

## Model

### SARIMAX

SARIMAX(p,d,q)(P,D,Q)[m]

where:

* p, d, q = non-seasonal parameters
* P, D, Q = seasonal parameters
* m = seasonal period

### Key Parameters

| Parameter                     | Value                           |
| ----------------------------- | ------------------------------- |
| Seasonal Period (m)           | 7                               |
| Non-Seasonal Differencing (d) | 0                               |
| Maximum p, q                  | 3                               |
| Maximum P, Q                  | 2                               |
| Model Selection               | AutoARIMA (AIC)                 |
| Variable Selection            | Backward Elimination (p ≤ 0.05) |
| Maximum Model Fits            | 20                              |

---

## Results Summary

### Stage 1 — Training Window Selection

* Optimal training window: **81 weeks**
* Best model: **SARIMAX(0,0,0)(2,0,0)[7]**
* RMSE: **225.35**
* MAPE: **4.7%**

### Stage 2 — Model Stability Analysis

* Most stable model: **SARIMAX(0,0,1)(2,0,2)[7]**
* Open, Promo, and day-of-week variables remained consistently important
* Highest forecasting errors occurred during Christmas and New Year periods

### Stage 3 — Training Data Length Analysis

* Evaluated training lengths from **1 to 12 months**
* Forecast accuracy improved as additional historical data was added
* Performance gains became marginal after approximately **6 months**
* Best performance achieved with **12 months** of training data

### Stage 4 — Network-Wide Forecasting

* Forecasting pipeline applied to **1,115 stores**
* Mean RMSE: **751.55**
* Median RMSE: **684.62**
* Only **47 stores (4.2%)** were identified as outliers
* Most common model structure was used by only **15.2%** of stores
* Results confirm the importance of **store-specific forecasting models**

### Additional Resources

* `Final_Presentation.pdf` — Complete visual analysis, forecasting plots, model diagnostics, and stage-wise results.

---
## Key Findings

- Weekly seasonality (m = 7) was the dominant forecasting pattern across the Rossmann network.
- Open and Promo were the most important predictors and were selected consistently across stores.
- Forecast accuracy improved as more historical data was added, but performance gains became marginal after approximately 6 months.
- No single SARIMAX specification fit all stores, highlighting the importance of store-level model selection.
- The forecasting pipeline was successfully applied across 1,115 stores using automated model and variable selection.
- Most forecasting outliers belonged to stores that are always closed on Sundays and exhibit more irregular sales behaviour.
---

## Tech Stack

| Tool         | Purpose                     |
| ------------ | --------------------------- |
| Python       | Core programming language   |
| pandas       | Data manipulation           |
| NumPy        | Numerical computation       |
| statsmodels  | SARIMAX estimation          |
| pmdarima     | AutoARIMA order selection   |
| scikit-learn | Forecast evaluation metrics |
| Matplotlib   | Visualisation               |

---

## Limitations

* AutoARIMA with backward elimination is computationally expensive at scale
* Only short-term forecasting was evaluated (14-day horizon)
* Weather, local events, and economic indicators were not included
* Stores were modelled independently
* Christmas and New Year periods consistently produced higher forecasting errors

---

## Future Work

* Incorporate external variables such as regional events etc.
* Explore hybrid SARIMAX + Machine Learning approaches
* Compare against models such as XGBoost and LSTM
* Extend forecasting horizons beyond 14 days
* Investigate hierarchical and clustered forecasting methods

---

## Citation

```text
Patil, S. (2026).
Forecasting Product Demand Using Multivariate Time Series Models.
Master's Thesis, Berliner Hochschule für Technik (BHT Berlin).
```

---


This project is intended for academic and educational purposes.

The Rossmann Store Sales dataset belongs to Rossmann and Kaggle. Please refer to the original Kaggle competition page for dataset usage terms.
