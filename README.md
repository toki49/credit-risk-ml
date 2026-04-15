# Credit Risk ML Project

## Project Overview

This project predicts whether a European firm will experience a credit rating downgrade within 12 months using company financial ratios and firm attributes. It compares a Logistic Regression baseline against a Random Forest classifier and evaluates both models with AUROC under rolling time-based validation.

## Dataset

The source data is a European corporate credit-risk Excel workbook containing roughly 121,000 companies split across 6 sheets. Fields used in this project are `Turnover`, `EBIT`, `PLTax`, `Leverage`, `ROE`, `TAsset`, `MScore`, `Region`, `Country`, `NACE code`, `Sector 1`, and yearly observations from 2015 to 2020.

The binary target is defined as `downgrade_flag = 1` if `MScore` worsens by more than `0.5` in the following year, and `0` otherwise.

## Project Structure

```text
credit-risk-project/
├── data/
│   ├── raw/              # original credit-risk.xlsx
│   └── processed/        # credit_panel.csv, features.csv
├── figures/              # roc_logreg.png, model_comparison.png, shap_summary.png
├── models/               # rf_fold1.pkl, rf_fold2.pkl
├── process_credit_risk.py
├── train_logreg_time_cv.py
├── train_rf_time_cv.py
├── feature_importance.py
├── README.md
└── requirements.txt
```

## Setup Instructions

### Windows

```powershell
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
```

### Mac/Linux

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

## How to Run

```bash
python process_credit_risk.py
```
Loads the raw Excel workbook, reshapes it into a company-year panel, engineers the downgrade target, and creates `data/processed/credit_panel.csv` and `data/processed/features.csv`.

```bash
python train_logreg_time_cv.py
```
Runs rolling time-based cross-validation for the Logistic Regression baseline and saves the ROC chart to `figures/roc_logreg.png`.

```bash
python train_rf_time_cv.py
```
Runs rolling time-based cross-validation for the Random Forest model, saves `rf_fold1.pkl` and `rf_fold2.pkl`, and creates `figures/model_comparison.png`.

```bash
python feature_importance.py
```
Loads the best Random Forest model and plots the top 15 built-in feature importances to `figures/shap_summary.png`.

## Results Summary

| Model         | Fold 1 AUROC | Fold 2 AUROC | Mean AUROC |
|---------------|--------------|--------------|------------|
| LogReg        | 0.6688       | 0.6487       | 0.6588     |
| Random Forest | 0.7061       | 0.6717       | 0.6889     |

The Random Forest outperformed the Logistic Regression baseline on both validation folds and achieved the strongest mean AUROC. This suggests that nonlinear interactions among financial ratios and credit-quality signals add predictive value beyond a linear baseline.

## Limitations and Future Work

- The feature set does not include macroeconomic drivers such as FRED or ECB interest-rate and credit-cycle variables.
- The dataset is European in scope, so findings may differ from the original U.S.-focused utility or credit applications the user may want to compare against.
- `MScore` is used as a downgrade proxy rather than true external rating agency downgrade events.
- Full SHAP analysis was skipped due to computation scale, so the project currently uses Random Forest built-in feature importances instead.
