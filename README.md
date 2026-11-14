# AgroMaster ML: Vegetable Price Risk Classification

Machine learning pipeline that classifies the 7-day-ahead price risk (Low / Medium / High) of perishable vegetables in Indian wholesale markets, using daily Agmarknet price records.

## Overview

| Item | Detail |
|---|---|
| Task | 3-class classification (Low / Medium / High price risk) |
| Horizon | 7 days ahead |
| Commodities | Tomato, Onion, Potato, Brinjal, Green Chilli |
| Data | Agmarknet daily mandi prices, 2001–2025 |
| Models | Logistic Regression, Gaussian Naive Bayes, Decision Tree, Random Forest, XGBoost |
| Main metrics | Macro F1, High-risk recall |

## Dataset

- **Source:** [Daily Commodity Prices India](https://www.kaggle.com/datasets/khandelwalmanas/daily-commodity-prices-india) (Kaggle, Agmarknet records)
- **Raw size:** 75,984,017 rows, 26 yearly Parquet files
- **Modelling subset:** 7,919,229 rows, 2,605 price series, 787 markets, 25 states
- **Series selection rule:** at least 1,500 reporting days and an 8-year span up to 2023-12-31

A series is one combination of state, district, market, commodity, variety and grade.

Data files are not stored in this repository. See `data/README.md` for download instructions.

## Method

**Target.** `ratio = modal_price(t+7) / mean(modal_price over the previous 90 days)`. Risk labels are assigned per commodity from the 10th and 30th percentiles of this ratio. Thresholds are fitted on training data only.

**Features.** 18 features built from past prices only (no look-ahead). See `notebooks/04_features_and_target.ipynb`.

**Split.** Chronological, no shuffling.

| Set | Period |
|---|---|
| Development | 2001-01 to 2023-12 |
| Final test | 2024-01 to 2025-11 |

**Validation.** Five expanding-window folds inside the development period. Thresholds, scalers, encoders and class weights are fitted on the training fold only.

**Preprocessing.** Scaling and one-hot encoding for Logistic Regression and Naive Bayes. Ordinal encoding for tree models.

**Class imbalance.** The High class is about 10% of rows, so class weights are used.

**Tuning.** Randomised search, selected on mean validation macro F1. Early stopping for XGBoost.

**Baselines.** Majority class and risk persistence.

**Final evaluation.** The selected model is refitted on the full development period and evaluated once on the test period.

## Repository structure

```
agromaster-ml/
├── README.md
├── requirements.txt
├── .gitignore
├── notebooks/
│   ├── 01_data_loading.ipynb
│   ├── 02_eda.ipynb
│   ├── 03_cleaning.ipynb
│   ├── 04_features_and_target.ipynb
│   ├── 05_split_labels.ipynb
│   ├── 06_baselines.ipynb
│   ├── 07_models.ipynb
│   ├── 08_evaluation.ipynb
│   └── 09_interpretation.ipynb
├── data/
│   └── README.md
├── reports/
│   ├── figures/
│   └── tables/
└── docs/
```

## Getting started

1. Open a notebook in Google Colab.
2. Mount Google Drive. Outputs are written to `MyDrive/agromaster/`.
3. Run the notebooks in order, 01 to 09. Each notebook reads the output of the previous one.

| Notebook | Output |
|---|---|
| 01_data_loading | `agmarknet_subset.parquet` |
| 03_cleaning | `agmarknet_clean.parquet` |
| 04_features_and_target | `features.parquet` |

Requirements: Python 3, pandas, numpy, pyarrow, scikit-learn, xgboost, shap, matplotlib, kagglehub.

## Project status

- [x] Data loading and subset
- [x] Exploratory data analysis
- [x] Cleaning
- [x] Features and target
- [x] Chronological split, folds and labels
- [x] Baselines
- [x] Model training and tuning
- [ ] Final test evaluation and comparison
- [ ] Interpretation (feature importance, SHAP, error analysis)
- [ ] Report and presentation

## Results

To be added after the final test evaluation.

## Limitations

- Records for the five selected commodities end in November 2025, so the test period is shorter than the full source range (which runs to April 2026).
- Reporting is incomplete in the last weeks of the data.
- Markets report on irregular days, so series contain gaps.

## Contributing

1. Create a branch from `development`, for example `08-evaluation`.
2. Save the notebook to that branch under `notebooks/`.
3. Open a pull request into `development`.
4. Delete the branch after merging.
