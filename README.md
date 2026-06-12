# TDA-LLA

Topological Data Analysis pipeline for relapse classification using persistence-based features.

---

## Overview

This repository implements a complete workflow to process cytometry data, extract topological features using persistent homology, and evaluate their predictive power for classification.

The pipeline is structured as a sequence of notebooks, from raw data preprocessing to machine learning models.

---

## Data

The dataset used in this study is publicly available at:

https://github.com/Almr95/Relapse-Prediction/tree/main/Selection_A

This dataset contains the cytometry measurements used to compute persistence diagrams and subsequent topological features.

The data is not redistributed in this repository. Please refer to the original source above.

### Selected patients

In this study, only a subset of patients corresponding to the **SEHOP–PETHEMA 2013 protocol** is considered.

The list of selected patients is provided in:

Data/selected_patients.xlsx

This file contains the identifiers of the samples included in the analysis, ensuring full reproducibility of the results.

Users should filter the dataset accordingly before running the pipeline.

---

## Pipeline Structure

### Step 1 — Data preprocessing
- **Step 1 - FCS to TXT.ipynb**
  - Converts raw `.fcs` files into `.txt`
  - Prepares data for topological analysis

---

### Step 2 — Persistence diagrams
- **Step 2 - Vietoris Rips.ipynb**
  - Computes Vietoris–Rips filtrations
  - Generates persistence diagrams:
    - H0 (connected components)
    - H1 (cycles)

---

### Step 3 — Grid search on Betti Curves
For Persistence Landscapes, Silhouettes and Persistence Images, equivalent notebooks are provided within their respective folders, following the same pipeline (Step 3.1 and Step 3.2) for both individual (H0, H1) and concatenated (H0+H1) representations.

In addition to the parameters explored for Betti curves, these representations include **descriptor-specific hyperparameters**, such as:
- Number of landscapes (for Persistence Landscapes)
- Resolution and weighting functions (for Persistence Images)
- Representation-specific smoothing and scaling parameters

#### Individual dimensions
- **Step 3.1 Grid-BC.ipynb**
  - Computes Betti curves for H0 and H1
  - Explores:
    - Resolution
    - Threshold (0 or percentile-based)
    - Normalization
    - Gaussian smoothing
  - Statistical evaluation:
    - Mann–Whitney U test
    - Fisher combination

#### Concatenated representation
- **Step 3.2 Grid (H0H1)-BC.ipynb**
  - Computes Betti curves for H0 + H1
  - Explores:
    - Resolution
    - Threshold (0 or percentile-based)
    - Normalization
    - Gaussian smoothing
  - Statistical evaluation:
    - Mann–Whitney U test
    - Fisher combination

---

### Step 4 — Feature generation (best configs)
Equivalent notebooks for Persistence Landscapes, Silhouettes and Persistence Images are available in their respective folders, following the same structure (Step 4.1 and Step 4.2) for both individual and concatenated representations.
#### Individual dimensions
- **Step 4.1 Generate Best -BC.ipynb**
  - Generates final Betti curves for H0 and H1
  - Uses best configuration from Step 3.1

#### Concatenated representation
- **Step 4.2 Generate Best (H0H1)-BC.ipynb**
  - Generates final features for H0 + H1
  - Uses best configuration from Step 3.2

---

### Step 5 — Classification

#### Random Forest
- **Step 5.1 Grid RF -BC.ipynb**
  - Grid search over:
    - n_estimators
    - max_depth
  - Evaluation:
    - ROC AUC
    - Accuracy
    - F2-score

#### Support Vector Machine
- **Step 5.2 Grid SVM -BC.ipynb**
  - Kernels:
    - linear, rbf, poly, sigmoid
  - Parameters:
    - C, gamma, degree, coef0
  - Evaluation:
    - ROC AUC
    - Accuracy
    - F2-score

---

## Methodology

### Feature extraction
- Betti curves computed from persistence diagrams  
- Preprocessing options:
  - Thresholding (0 or p10)
  - Normalization (none, L1)
  - Gaussian smoothing

### Representations
- **H0** — connected components  
- **H1** — cycles  
- **H0H1** — concatenation of both  

### Statistical evaluation
- Mann–Whitney U test  
- Fisher method for p-value aggregation  

### Machine learning
Pipeline:
- Oversampling: `RandomOverSampler`  
- Models:
  - Random Forest  
  - SVM  

### Validation
- Stratified cross-validation (6 folds)  
- Oversampling applied **only to training folds**  
- No data leakage  

Additional descriptors (Persistence Landscapes, Silhouettes and Persistence Images) are computed following the same preprocessing and evaluation framework, ensuring direct comparability across representations.

---

## How to Run

Execute notebooks in order:
Step 1 → Step 2 → Step 3 → Step 4 → Step 5

Each step depends on outputs from the previous one.

---

## Outputs


- Topological features (Betti curves, Persistence Landscapes, Persistence Silhouettes, Persistence Images)
- Grid search results (CSV)  
- Model evaluation metrics  
- Confusion matrices  

---

## Notes

- SVM uses `probability=True` to compute AUC  
- Random Forest uses `class_weight="balanced"`  
- Oversampling is handled inside the cross-validation pipeline  
- Confusion matrices are provided for interpretation

---
