# Recovering Stellar Classes from Photometric Structure in SDSS

Can stars, galaxies, and quasars be distinguished using photometric measurements alone?

This project studies the multivariate structure of Sloan Digital Sky Survey (SDSS) observations and compares unsupervised and supervised approaches for recovering spectroscopic object classes. I examine whether five broadband photometric measurements contain enough information to distinguish stars, galaxies, and quasars, and quantify how much additional predictive information is provided by redshift.

## Overview

The analysis addresses four main questions:

1. Do the five photometric magnitudes (`u`, `g`, `r`, `i`, `z`) exhibit a low-dimensional structure?
2. Can unsupervised clustering recover the known spectroscopic classes?
3. How accurately can supervised models classify stars, galaxies, and quasars using photometric features alone?
4. How much does adding redshift improve classification performance?

The project combines dimensionality reduction, clustering, multiclass classification, and cross-validation to compare the structure revealed by unlabeled data with the structure learned under supervision.

## Data

The dataset contains **10,000 observations** from the Sloan Digital Sky Survey (SDSS) Data Release 14.

Each observation is labeled as one of three spectroscopic classes:

- **Galaxy:** 50.0%
- **Star:** 41.5%
- **Quasar (QSO):** 8.5%

The primary feature set consists of five photometric magnitudes:

- `u`
- `g`
- `r`
- `i`
- `z`

Redshift is treated separately to evaluate whether it provides information beyond broadband photometry.

Survey metadata and sky-coordinate variables were excluded from the core analysis to focus on features with direct relevance to object classification. Continuous predictors were standardized before scale-sensitive methods were applied.

## Methods

### Exploratory Analysis

I first examined feature distributions and dependence structure across the photometric variables and redshift.

The five photometric bands are strongly correlated, suggesting substantial redundancy and motivating dimensionality reduction.

### Principal Component Analysis

PCA was applied to the standardized photometric features to characterize their dominant low-dimensional structure.

- **PC1:** 87.5% of total variance
- **PC2:** 11.5% of total variance
- **PC1 + PC2:** approximately **99% of total variance**

The reduced space showed meaningful class structure, but stars and galaxies remained substantially overlapping.

### Unsupervised Clustering

I compared two clustering approaches using the first two principal components:

- Gaussian Mixture Models (GMM)
- K-means clustering

GMM was used as the primary method because it allows cluster-specific covariance structures and probabilistic assignments.

Although the clustering methods recovered broad geometric structure in the data, neither produced three clean groups corresponding to the known spectroscopic classes. In particular, stars and galaxies remained strongly mixed, and quasars did not form a fully isolated cluster.

### Supervised Classification

I then evaluated whether the class information could be recovered more effectively when labels were used directly.

Two classifiers were compared:

- Multinomial Logistic Regression
- Linear Discriminant Analysis (LDA)

Each model was evaluated under two feature sets:

1. Photometric magnitudes only
2. Photometric magnitudes + redshift

Five-fold stratified cross-validation was used to evaluate the stability of the results.

## Key Results

### Photometric features already contain strong class information

Using only the five photometric magnitudes, multinomial logistic regression achieved:

| Metric | Performance |
|---|---:|
| Cross-validated accuracy | **93.3% ± 0.1%** |
| Star recall | **95.4% ± 0.3%** |
| Galaxy recall | **92.6% ± 0.6%** |
| QSO recall | **87.1% ± 2.8%** |

Despite substantial overlap in the unsupervised PCA and clustering spaces, supervised models were able to recover the class labels with high accuracy.

### Redshift provides substantial additional information

Adding redshift increased logistic-regression performance to:

| Metric | Photometric Only | + Redshift |
|---|---:|---:|
| Accuracy | 93.3% | **97.1%** |
| Star recall | 95.4% | **99.4%** |
| Galaxy recall | 92.6% | **95.8%** |
| QSO recall | 87.1% | **94.1%** |

The largest improvement occurred for quasars, which were the least represented and most difficult class to distinguish using photometry alone.

These gains were stable across cross-validation folds, indicating that redshift contributes complementary discriminatory information rather than producing an improvement specific to a single train-test split.

## Main Takeaways

- The five SDSS photometric bands are highly redundant and can be represented almost entirely by two principal components.
- Low-dimensional structure does **not** necessarily imply clean cluster structure: GMM and K-means only partially recovered the known object classes.
- Class information is much more effectively recovered under supervision.
- Photometric measurements alone support strong multiclass prediction.
- Redshift adds information that is not captured by broadband photometry and substantially improves classification, particularly for quasars.

This contrast between unsupervised structure and supervised predictability is the central result of the project.

## Repository Structure

```text
sdss-stellar-classification/
│
├── README.md
├── report.pdf
│
├── data/
│   └── data-readme.md
│
├── notebooks/
│   └── sdss_analysis.ipynb
│
└── figures/
    ├── feature_distributions.png
    ├── correlation_matrix.png
    ├── pca_scores.png
    ├── gmm_clusters.png
    ├── confusion_matrix.png
    └── model_comparison.png
