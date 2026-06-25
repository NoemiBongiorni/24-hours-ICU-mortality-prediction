<!-- omit from toc -->
# ICU Mortality Prediction: a Risk Stratification Analysis on the eICU Database
Project for the _Statistical Learning for Healthcare Data_ course held at Politecnico di Milano in the academic year 2025-2026 by Professor M. Ferrario and Professor L. Cavinato.

<!-- omit from toc -->
# Table of contents
- [Installing](#installing)
  - [Prerequisites](#prerequisites)
  - [Cloning the Repository](#cloning-the-repository)
  - [Installing the Libraries](#installing-the-libraries)
  - [Data Access](#data-access)
- [Project](#project)
- [Results](#results)
- [Structure](#structure)
- [Authors](#authors)

# Installing
## Prerequisites
- Python 3.x
- Pip (for installing Python libraries)

## Cloning the Repository
1. Clone the repository to your local machine using the following command:
```bash
git clone https://github.com/NoemiBongiorni/eicu-mortality-prediction
```
2. Navigate to the project directory using the following command:
```bash
cd eicu-mortality-prediction
```

## Installing the Libraries
With Pip, install the required libraries by running the following command:
```bash
pip install -r requirements.txt
```

## Data Access
The dataset used in this project requires completing a credentialing process on [PhysioNet](https://physionet.org/) before access is granted. Once access is obtained, the following tables must be placed in the [`data`](./data/) folder: `patient.csv`, `diagnosis.csv`, `lab.csv`, `intakeOutput.csv`, `vitalAperiodic.csv`, `vitalPeriodic.csv`, `infusionDrug.csv` and `hospital.csv`.

# Project
**Objective:** predict in-hospital mortality using only data collected during the first 24 hours of ICU admission, formulated as a binary classification task.

**Dataset:** 2,520 ICU records from the eICU Collaborative Research Database (US hospitals, 2014-2015), filtered down to a final cohort of 1,003 patients present across 7 source tables (demographics, diagnoses, labs, intake/output, periodic and aperiodic vital signs), described by 80 engineered features.

**Methodology:**
  - Built a patient-level feature matrix through table-specific extraction and aggregation: priority-weighted one-hot encoding of ICD-9 diagnosis macro-categories, unit standardization and mean/min/max aggregation of lab and vital sign measurements, and derivation of clinically meaningful indicators (BMI, pulse pressure).
  - Performed EDA and correlation analysis (Pearson, Cramér's V, ANOVA eta²) to identify and remove redundant, highly correlated variables.
  - Built a leakage-free preprocessing pipeline (MICE imputation, z-score normalization) fitted on the training set only, followed by mRMR feature selection.
  - Trained and tuned Lasso Logistic Regression, Random Forest, SVM and XGBoost via grid search and 5-fold stratified cross-validation, optimizing PR-AUC under class weighting to address the strong class imbalance (~10% mortality).
  - Tuned classification thresholds by maximizing the F1.5-score, then built and compared hard- and soft-voting ensembles.
  - Interpreted the final model through Lasso coefficients, permutation importance and SHAP values, and assessed performance across demographic and hospital subgroups.

# Results
The final model is a **soft-voting ensemble of Lasso Logistic Regression and Random Forest**, trained on the 40 features selected via mRMR. On the held-out test set, it achieved a **recall of 0.80** and a **precision of 0.18**, correctly identifying 16 out of 20 deceased patients, reflecting the deliberate threshold tuning toward sensitivity. Age, maximum systolic blood pressure, RDW%, heart rate and respiratory rate emerged as the strongest mortality risk factors, consistently across Lasso coefficients, permutation importance and SHAP analysis.

You can check out the final [`report.pdf`](./report/report.pdf).

# Structure
The repository contains the notebook [`Code.ipynb`](./Code.ipynb), which covers the full pipeline end-to-end:
1. **Data loading and cleaning**: table-by-table extraction, filtering and aggregation of the 8 source datasets, merged into a single patient-level dataset.
2. **Exploratory data analysis and preprocessing**: correlation analysis, outlier handling, train/validation/test split, MICE imputation and mRMR feature selection.
3. **Model implementation**: training, hyperparameter tuning, threshold optimization and ensemble construction for Lasso, Random Forest, SVM and XGBoost.
4. **Model interpretability**: Lasso coefficients, permutation importance, SHAP analysis and stratified performance evaluation across demographic and hospital subgroups.

# Authors
- Noemi Bongiorni ([@NoemiBongiorni](https://github.com/NoemiBongiorni))
- Sara Redaelli ([@sarareda17](https://github.com/sarareda17))
- Rachele Zanin ([@rachelezanin](https://github.com/rachelezanin))
