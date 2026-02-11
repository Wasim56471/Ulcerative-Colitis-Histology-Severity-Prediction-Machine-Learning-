# Ulcerative Colitis Histology Severity Prediction (Machine Learning)

Predicting **ulcerative colitis (UC) histology severity** from **engineered cellular and spatial features** using classical machine learning, with **leakage-safe (subject-wise) evaluation**.

## Project Overview
This project builds and compares ML models for two related tasks:

1) **Severity score regression**  
   - Target: **Severity Score (0–5)**

2) **Inflammation detection (binary classification)**  
   - Label: **No inflammation (< 3)** vs **Inflammation (≥ 3)**

A key focus is **avoiding subject-level leakage** by using **GroupKFold** grouped by **PatID**, ensuring that records from the same subject never appear in both train and validation folds.

---

## Dataset (High level)
- **Subjects:** 106 unique patients (`PatID`)
- **Observations:** multiple longitudinal assessments per subject (up to ~3 per subject)
- **Predictors:** engineered **cellular composition** + **spatial structure** descriptors (high-dimensional, numeric feature table)
- **Preprocessing highlights:**
  - drop rows with missing target
  - drop rows where all predictors are missing
  - keep numeric predictors only
  - mask invalid negative values to `NaN`
  - median imputation inside the CV pipeline (leakage-safe)

> Note: The dataset file is **not included** in this repository.

---

## Methods
### Cross-validation (leakage-safe)
- **5-fold GroupKFold** with groups = `PatID`

### Models
**Regression**
- Ridge Regression
- Support Vector Regression (SVR, RBF kernel)
- Random Forest Regressor

**Classification**
- Logistic Regression
- Support Vector Classifier (SVC, RBF kernel)
- Random Forest Classifier

### Feature configurations
- **All features**
- **10 selected features**, selected **inside each CV fold** using `SelectFromModel` (Random Forest-based), to prevent feature-selection leakage.

### Metrics
**Regression**
- MAE (Mean Absolute Error)

**Classification**
- Accuracy
- ROC-AUC
- Sensitivity
- Specificity

---

## Results Summary (5-fold GroupKFold)
### Regression (MAE, lower is better)
| Model | All features (MAE) | 10 features (MAE) |
|------|---------------------|-------------------|
| Ridge | 1.0832 ± 0.0946 | 1.1919 ± 0.0946 |
| SVR | 0.9277 ± 0.0797 | 0.7401 ± 0.0600 |
| Random Forest | 0.6929 ± 0.0340 | 0.6826 ± 0.0416 |

### Classification (Accuracy / ROC-AUC)
| Model | Setting | Accuracy | ROC-AUC |
|------|---------|----------|---------|
| Logistic Regression | All features | 0.9009 ± 0.0236 | 0.9434 ± 0.0180 |
| SVC | All features | 0.8976 ± 0.0481 | 0.9469 ± 0.0256 |
| Random Forest | All features | 0.9273 ± 0.0137 | 0.9827 ± 0.0044 |
| Logistic Regression | 10 features | 0.8977 ± 0.0123 | 0.9759 ± 0.0077 |
| SVC | 10 features | 0.9175 ± 0.0183 | 0.9755 ± 0.0082 |
| Random Forest | 10 features | 0.9306 ± 0.0341 | 0.9811 ± 0.0075 |

---

## Repository Contents
- `Machine_Learning_CW1_Code.ipynb` — full notebook (data checks, preprocessing, pipelines, CV evaluation, plots)
- `Machine Learning Coursework.pdf` — accompanying report

---

## How to Run

### Option A — Google Colab (recommended)
1. Open `Machine_Learning_CW1_Code.ipynb` in Colab
2. Put your dataset Excel file in Google Drive
3. Update the path in the notebook:
   ```python
   file_path = "/content/drive/MyDrive/CSI_7_MAL_2526_Data.xlsx"
