# Delivery Time Prediction and Delivery Pattern Analysis

> Portfolio Project 02 · 2026
> A comparative machine learning study of delivery-time prediction and delivery-pattern segmentation.
>
> Status: Completed exploratory project

## Project Overview

The project uses delivery records containing information about courier characteristics, delivery distance, pickup waiting time, weather, traffic, vehicle type, area type, product category, and delivery time.

The workflow covers data preparation, feature-representation comparison, supervised model evaluation, and exploratory K-means clustering.

## Research Questions

1. How accurately can delivery time be predicted from courier, order, environmental, vehicle, area, and distance variables?
2. Which feature representation performs better for this prediction task: score-based encoding or one-hot encoding?
3. Can unsupervised clustering reveal interpretable delivery patterns beyond a single delivery-time prediction?

## Methodology

### 1. Data Preparation

The raw delivery records were prepared through the following steps:

* Removed identifiers and fields that were not used as model inputs
* Calculated delivery distance from store and drop-off coordinates using the Haversine formula
* Calculated pickup waiting time from order and pickup timestamps
* Removed the original coordinate and time fields after deriving the required features
* Cleaned categorical values and handled missing observations

### 2. Feature Representation and Dataset Validation

Categorical variables were represented in two ways:

- **Score-based encoding:** `Traffic` and `Weather` categories were converted into ordered scores
- **One-hot encoding:** `Traffic` and `Weather` categories were represented as separate binary variables

`Vehicle`, `Area`, and `Category` had already been one-hot encoded during the earlier data-preparation stage. Therefore, the score-based datasets combine score-encoded traffic and weather variables with one-hot-encoded remaining categorical variables, whereas the one-hot datasets use one-hot encoding for all categorical variables.

Two feature scopes were compared:

- **All features:** all available preprocessed predictors were retained
- **Structural subset:** selected distance, pickup-waiting-time, traffic, weather, vehicle, and area variables were retained while other predictors, such as category indicators, were excluded

Random Forest regression with five-fold cross-validation was used to compare the four candidate datasets before selecting the representation for the subsequent modeling stage.

### 3. Supervised Delivery-Time Prediction

Two nonlinear regression models were trained and evaluated:

* **Random Forest Regression** — evaluated using out-of-bag validation, cross-validation, and a held-out test set
* **XGBoost Regression** — tuned using Optuna and evaluated on the held-out test set

Model interpretation included:

* Actual-versus-predicted plots
* Residual plots and residual distributions
* Feature-importance analysis
* Partial dependence and individual conditional expectation plots
* Error-regime analysis for high-error observations

### 4. Unsupervised Delivery-Pattern Analysis

K-means clustering was used to explore whether deliveries could be grouped according to their feature profiles.

Two clustering settings were examined:

* A representation including product-category variables
* A representation excluding product-category variables

The analysis used standardized features and compared candidate numbers of clusters using inertia and silhouette-based diagnostics. Cluster lineages, delivery-time distributions, and feature profiles were then visualized through Sankey diagrams, distribution plots, and cluster-level comparisons.

## Results Summary

The recorded experiments are reported by evaluation stage: cross-validation for dataset and model selection, and held-out test results for final predictive performance.

### Feature Representation Comparison

The four candidate datasets produced the following five-fold cross-validation RMSE values:

| Representation | Feature set | 5-fold CV RMSE |
| --- | --- | ---: |
| Score-based | All features | 22.911 |
| One-hot | All features | 23.201 |
| Score-based | Structural subset | 46.211 |
| One-hot | Structural subset | 46.334 |

The score-based all-feature representation achieved the lowest cross-validation RMSE and was used for the subsequent prediction models.

### Model Validation Results

The validation-stage results were as follows:

| Model | Validation procedure | RMSE |
| --- | --- | ---: |
| Random Forest | Out-of-bag validation | 22.85 |
| Random Forest | Five-fold cross-validation | 22.90 |
| XGBoost | Optuna cross-validation | 22.07 |

### Held-out Test Performance

| Model | Test RMSE |
| --- | ---: |
| Random Forest | 22.10 |
| XGBoost | 22.00 |

XGBoost produced a slightly lower test RMSE than Random Forest on the held-out test split. The difference is small and should not be interpreted as a substantial model improvement.

## Limitations

Several limitations should be considered:

* The dataset is a single delivery dataset, so the recorded performance should not be interpreted as generalizable performance across other platforms or regions.
* Score-based encoding imposes an order on traffic and weather categories. This assumption was compared with one-hot encoding but remains a modeling choice.
* Clustering is exploratory: results may change depending on feature scaling, the selected variables, and the number of clusters, and were not evaluated as a held-out prediction task.
* The repository does not include external validation data.

## Repository Contents

* `notebooks/` — preprocessing, encoding, dataset validation, prediction, and clustering notebooks
* `results/random_forest/` — Random Forest evaluation and interpretation figures
* `results/xgboost/` — XGBoost evaluation and interpretation figures
* `results/k_means/` — clustering diagnostics and cluster-profile figures

## Data Availability

Raw and processed datasets are intentionally excluded from this public repository. The original data are referenced locally as `amazon_delivery_raw.csv`, and the dataset source and redistribution terms are not documented in this repository. Reproducing the results requires obtaining the source data separately and placing the files under the corresponding local directories: `data/01_raw/`, `data/02_processed/sandbox/`, and `data/02_processed/production/`.

## Language

The notebook comments and original analysis notes are primarily written in Korean, while the README and result figure titles are written in English.
