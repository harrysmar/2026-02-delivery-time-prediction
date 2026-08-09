# Delivery Time Prediction and Delivery Pattern Analysis

> Portfolio Project 02 · 2026
> A comparative machine learning study of delivery-time prediction and delivery-pattern segmentation.
>
> Status: Completed exploratory project

This repository contains an exploratory machine learning project focused on two related questions: how accurately delivery time can be predicted from order and delivery conditions, and whether recurring delivery patterns can be identified through unsupervised learning.

## Project Overview

The project uses delivery records containing information about courier characteristics, delivery distance, pickup waiting time, weather, traffic, vehicle type, area type, product category, and delivery time.

The analysis was designed to move from data preparation to model comparison. Categorical variables were represented in multiple ways, and the resulting datasets were evaluated before training nonlinear prediction models. The project then extended beyond point prediction by using clustering methods to examine groups of deliveries with similar feature profiles.

The repository therefore preserves both the predictive modeling workflow and the exploratory analysis of delivery regimes.

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

The resulting features included courier characteristics, delivery distance, pickup waiting time, environmental conditions, vehicle type, area type, and product category.

### 2. Feature Representation and Dataset Validation

Categorical variables were represented in two ways:

* **Score-based encoding** — traffic and weather categories were converted into ordered scores
* **One-hot encoding** — categorical levels were represented as separate binary variables

In this project, these labels refer specifically to the representation of the `Traffic` and `Weather` variables. `Vehicle`, `Area`, and `Category` had already been one-hot encoded during the earlier data-preparation stage. Therefore, the score-based datasets combine score-encoded traffic and weather variables with one-hot-encoded categorical variables, whereas the one-hot datasets use one-hot encoding for all categorical variables.

For each encoding approach, the project compared an all-feature representation with a structural subset containing selected delivery-related variables. Random Forest regression with five-fold cross-validation was used to compare the candidate datasets before selecting the representation for the subsequent modeling stage.

Here, **all features** means that all available preprocessed predictors were retained. **Structural subset** means that the analysis used the selected distance, pickup-waiting-time, traffic, weather, vehicle, and area variables while excluding other predictors such as category indicators.

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

The recorded experiments are separated by evaluation purpose. Cross-validation results were used for dataset and model selection, while the held-out test results summarize final predictive performance.

### Feature Representation Comparison

The candidate feature representations were compared using five-fold cross-validation with Random Forest regression. In the table below, `score-based` and `one-hot` describe the traffic and weather representation, while `all features` and `structural subset` describe the predictor scope.

| Representation | Feature set | 5-fold CV RMSE |
| --- | --- | ---: |
| Score-based | All features | 22.911 |
| One-hot | All features | 23.201 |
| Score-based | Structural subset | 46.211 |
| One-hot | Structural subset | 46.334 |

The score-based all-feature representation achieved the lowest cross-validation RMSE and was used for the subsequent prediction models.

### Model Validation Results

These values come from the model-validation and hyperparameter-tuning stages. They are not held-out test results.

| Model | Validation procedure | RMSE |
| --- | --- | ---: |
| Random Forest | Out-of-bag validation | 22.85 |
| Random Forest | Five-fold cross-validation | 22.90 |
| XGBoost | Optuna cross-validation | 22.07 |

### Held-out Test Performance

| Model | Test RMSE | Test MAE |
| --- | ---: | ---: |
| Random Forest | 22.10 | 17.10 |
| XGBoost | 22.00 | Not reported |

XGBoost produced a slightly lower test RMSE than Random Forest on the held-out test split. The difference is small, so the result should be interpreted as comparable predictive performance rather than a substantial model improvement.

The prediction models showed similar overall error levels, while the clustering analyses provided an exploratory view of delivery groups with different feature profiles and delivery-time distributions.

## Project Status and Limitations

The project was completed through data preprocessing, feature-representation comparison, supervised model evaluation, and exploratory clustering. It is preserved as an analytical portfolio project rather than a deployed prediction service.

Several limitations should be considered:

* The dataset is a single delivery dataset, so the recorded performance should not be interpreted as generalizable performance across other platforms or regions.
* Score-based encoding imposes an order on traffic and weather categories. This assumption was compared with one-hot encoding but remains a modeling choice.
* The clustering analysis is exploratory. Results can change depending on feature scaling, the selected variables, and the number of clusters.
* Clustering was used for pattern discovery rather than held-out predictive evaluation.
* The repository does not include external validation data or a deployed inference pipeline.

## Repository Contents

* `notebooks/` — preprocessing, encoding, dataset validation, prediction, and clustering notebooks
* `results/random_forest/` — Random Forest evaluation and interpretation figures
* `results/xgboost/` — XGBoost evaluation and interpretation figures
* `results/k_means/` — clustering diagnostics and cluster-profile figures

## Data Availability

Raw and processed datasets are intentionally excluded from this public repository. The original data are referenced locally as `amazon_delivery_raw.csv`, and the dataset source and redistribution terms are not documented in this repository.

The notebooks are preserved to document the analytical workflow. Reproducing the results requires obtaining the source data separately and placing the files under the corresponding local directories: `data/01_raw/`, `data/02_processed/sandbox/`, and `data/02_processed/production/`.

## Language

The notebook comments and original analysis notes are primarily written in Korean, while the README and result figure titles are written in English.
