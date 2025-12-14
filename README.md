# P10 - Smart Cash Management Solution for SMEs

**Authors (T1):**

- Marlene Ibrus
- Maare Karmen Oras
- Aleksander Volzinski

## Overview

Small and medium-sized enterprises (SMEs) often keep excess cash in low-interest operating accounts due to limited visibility into future cash needs and the risk of liquidity shortages. As a result, they miss opportunities to earn higher returns through savings accounts, money market products, or overnight deposits.

This project presents a smart cash management proof-of-concept that helps SMEs optimize liquidity automatically. Using historical transaction data, the system forecasts short-term cash flows and identifies surplus funds that can be safely moved.

## File Structure

project-root/
- data/ – contains datasets used for the project

  - [synthetic_sme_customers_processed.csv](https://github.com/marleneibrus/ML-SEB/blob/main/Data/synthetic_sme_customers_processed.csv) – customer data

  - [synthetic_sme_transactions_processed.csv](https://github.com/marleneibrus/ML-SEB/blob/main/Data/synthetic_sme_transactions_processed.csv) – transaction data

- notebooks/ – Jupyter notebooks used for analysis and modeling

  - [data_preparation.ipynb](https://github.com/marleneibrus/ML-SEB/blob/main/Notebooks/data_preparation.ipynb) – data cleaning, preprocessing and exploratory analysis

  - [data_understanding.ipynb](https://github.com/marleneibrus/ML-SEB/blob/main/Notebooks/data_understanding.ipynb) – data cleaning, preprocessing and exploratory analysis

  - [global_xgb_pipeline.ipynb](https://github.com/marleneibrus/ML-SEB/blob/main/Notebooks/global_xgb_pipeline.ipynb) - attempt at a universal `xgboost` model

  - [project_final.ipynb](https://github.com/marleneibrus/ML-SEB/blob/main/Notebooks/project_final.ipynb) – forecasting models (SARIMA/ARIMA)
 
  - [segmented_xgb_pipeline.ipynb](https://github.com/marleneibrus/ML-SEB/blob/main/Notebooks/segmented_xgb_pipeline.ipynb) - attempt at a segmented `xgboost` model with clustering

- README.md – project overview, methodology, and results

- `...` - additional files


## **Dataset**

- Transactions: 2,540,072

- SMEs: 958

- Transaction range: -€163,541.87 to €3,997,185.92

- Average transaction: €1,442.36 (skewed due to outliers)

Preparation steps:

- Standardized amounts to EUR (2 decimals)

- Converted timestamps to datetime

- Mapped debit/credit to inflows/outflows

- Removed micro-transactions (< €0.01)

- Computed daily net flows per SME

- Winsorized top/bottom 1% to reduce outlier impact


## Exploratory Data Analysis (EDA)

Key insights:

- Cash flows are highly volatile and skewed

- Outflows slightly exceed inflows overall

- Only a subset of SMEs show consistent, predictable patterns suitable for modeling


## Customer Selection

Filtered SMEs to ensure reliable predictions:

- Minimum 60 daily observations

- Average daily flow ≥ €500

- Flow range ≥ €1,000

**Result**: 78 SMEs (8.1%) selected for modeling.


## Forecasting Approach

Final Model: **SARIMA**

- Captures weekly seasonality (period=7)

- Accounts for autocorrelation and trends

- Works well with limited historical data

- Provides interpretable predictions

Modeling steps:

1. Checked stationarity using ADF test

2. Parameter selection via grid search over (p,d,q) × (P,D,Q,s)

3. Forecasted 7-day net cash flows

4. Evaluated using RMSE


## Prediction Performance

- Only 4 out of 78 SMEs (5.1%) achieved RMSE ≤ €1,000

- Most SMEs were difficult to predict due to volatility

- Prediction accuracy was not strongly correlated with transaction volume or AIC

Insight: Accurate forecasts are feasible but limited to a small subset of SMEs.


## Safe-to-Move Surplus Estimation

Estimated surplus funds per SME based on:

- Current estimated balance

- Forecasted cash flows

- Safety buffer (historical outflows × factor)

- Lower confidence bounds

Preliminary total: €189,782,275.98

Important note: This is likely an overestimate, as actual balances before the dataset start are unknown. Real-world safe-to-move funds would be lower.


## Visualizations

- RMSE Distribution: Most SMEs exceed €5,000

- Safe-to-Move vs Accuracy: High surplus does not guarantee reliable prediction

- Transfer Validity: Average 72.9% of forecast days show safe surplus

- Model Fit (AIC) vs Accuracy: No strong correlation


## Transfer Recommendation Strategy

- Strong Recommendation: Accurate forecast (RMSE ≤ €1,000) + frequent surplus

- Limited Recommendation: Surplus present but forecast unreliable

- Not Recommended: No reliable surplus

Only 4 SMEs qualify for strong recommendations.


## Key Learnings

1. SME cash flow is highly volatile → prediction is challenging

2. Transaction data alone is insufficient → need starting balances and context

3. Customer segmentation is crucial → one-size-fits-all models fail

4. Safe-to-move surplus estimates require conservative buffers and caps

Human oversight is essential for large transfers
