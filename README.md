                    infant-skin-microbiome--disease-classification--ML-Model--k-NN

Description of the project: k-NN classification of infant skin microbiome profiles across time points and sampling skin sites, following biologically constrained preprocessing.

# Infant Skin Microbiome Disease Classification (k-NN)

This repository contains Machine-learning models for classifying infant clinical disease groups using skin microbiome abundance profiles. Models are trained separately by infant age and skin sampling site to preserve biological validity and avoid confounding across heterogeneous sampling conditions.

## Project overview

Infant skin microbiome composition varies substantially with age, anatomical site, and disease state.
This project evaluates whether microbial abundance profiles can distinguish four clinical groups using a k-nearest neighbors (k-NN) classifier under biologically constrained preprocessing.

Clinical prediction task:

| Label | Clinical group     |
| ----- | ------------------ |
| 1     | Food allergy       |
| 2     | Food sensitization |
| 3     | Eczema             |
| 4     | Healthy control    |

Models are built independently for each age–site combination:

* T1–Ac: 2–3 months, antecubital fossa
* T1–Ch: 2–3 months, cheek
* T3–Ac: 1 year, antecubital fossa
* T3–Ch: 1 year, cheek

This separation is deliberate and central to the project design.

---

## Dataset description

Two data tables are used throughout all notebooks.

### Vitality_microbialAbundance_table.csv

This table contains the microbiome feature matrix.

* Each row represents one skin swab sample.
* Columns represent different microbial abundance features derived from sequencing.
* The first column is a unique sample identifier and is renamed to 'met_id'.

This table provides the input features (X) for all models.

### Vitality_metadata_table.csv

This table contains clinical and sampling metadata.

Key columns used:

* 'met_id': sample identifier (used for merging)
* 'subject_id': infant identifier
* 'visit': infant age at sampling

  * T1 = 2–3 months
  * T3 = 1 year
* 'site_specific': skin site

  * Ac = antecubital fossa
  * Ch = cheek
* 'group': clinical disease label (1–4)
  
* 'group_eczemaYesOrNo': disease group plus eczema presence

  * example: '3_yes', '4_no'
    
* 'group_eczema_affectedSkin': disease group, eczema presence, and whether the sampled site is affected with lesions.

  * example: '3_yes_no', '4_no_no'

Metadata columns are not used as features. They are used only for:

* subsetting samples
* auditing misclassifications

---

## Dataset size and model subsets

After merging the abundance and metadata tables:

* Total merged samples: 1,068
* Total columns after merge: 935

Subsets used for modeling:

| Model | Age        | Site              | Samples |
| ----- | ---------- | ----------------- | ------- |
| T1–Ac | 2–3 months | Antecubital fossa | 79      |
| T1–Ch | 2–3 months | Cheek             | 79      |
| T3–Ac | 1 year     | Antecubital fossa | 490     |
| T3–Ch | 1 year     | Cheek             | 420     |

Each subset is split using a stratified 70/30 train–test ratio.

---

## Modeling workflow

All notebooks follow the same pipeline:

1. Load abundance and metadata tables
2. Merge on met_id.
3. Subset by one age + one skin site
4. Drop all metadata columns from the feature matrix
5. Train–test split (stratified)
6. Median / standard-deviation scaling

   * no log transform
   * zero-variance features handled safely
7. Distance-weighted k-NN classification
8. Evaluation and interpretation

No dimensionality reduction, feature selection, or label engineering was performed.

---

## Model evaluation outputs

Each notebook reports:

* Macro-averaged F1 score
* Confusion matrix
* Full classification report
* One-vs-rest ROC curves
* 2-D PCA visualization of the test set
* Table of misclassified samples with clinical metadata

### Macro F1 scores (test set)

| Model | Macro F1 |
| ----- | -------- |
| T1–Ac | 0.28     |
| T1–Ch | 0.29     |
| T3–Ac | 0.28     |
| T3–Ch | 0.31     |

---

## Interpreting misclassifications

Misclassified samples are explicitly printed with:

* clinical group
* eczema presence
* whether the sampled skin site is affected

This allows direct biological inspection of errors such as:

* Eczema patients sampled at unaffected sites
* microbiome overlap between eczema and food-allergy groups
* site-specific ambiguity in early infancy

These outputs are intended for interpretation, not optimization.

---

## Repository contents

* Jupyter notebooks implementing one model per age–site combination
* Evaluation plots (ROC curves, PCA projections)
* This README documents data context and modeling decisions

Raw data files are confidential and are not included in this repository.

---

## Intended use

This repository is designed as:

* a reproducible record of an applied microbiome ML analysis
* a demonstration of biologically constrained modeling practices
* a research portfolio artifact

It is not intended as a production classifier or benchmark model.

---

## Author
Faizan Shafeer

---
