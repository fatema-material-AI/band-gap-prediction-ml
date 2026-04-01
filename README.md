# Band Gap Prediction using Machine Learning (Explainable AI Approach)

## Overview

This project explores band gap prediction in materials using machine learning, with a focus on interpretability and scientific reasoning.

## Objectives

* Predict band gap from material descriptors
* Compare baseline and advanced machine learning models
* Use explainable AI methods to identify important features
* Connect machine learning outputs with physical interpretation

## Features Used

The model used composition- and structure-related descriptors such as:

* electronegativity difference
* atomic radius average
* density

Additional engineered features were created to capture non-linear and interaction effects, including:

* density squared
* electronegativity squared
* atomic radius squared
* electronegativity–radius interaction
* density–electronegativity interaction
* density–radius interaction

## Models Tested

* Linear Regression
* Random Forest Regressor
* Tuned Random Forest Regressor
* Gradient Boosting Regressor

## Key Results

* Baseline linear model MAE: ~0.77
* Random Forest MAE: ~0.51
* Tuned Random Forest MAE: ~0.50
* Gradient Boosting MAE: ~0.75

The tuned Random Forest achieved the best performance on this dataset.

## Explainable AI Findings

Feature importance analysis showed that the most influential features were:

1. density
2. density squared
3. electronegativity–radius interaction

This suggests that band gap in this dataset is strongly influenced by structural effects such as atomic packing, along with combined structural and electronic factors.

## Validation Experiments

To test whether the model was overly dependent on density, density-related features were removed and the model was retrained.

* MAE without density features: ~1.10

This large increase in error confirmed that density was not just a model shortcut, but a genuinely important predictive feature in this dataset.

## Interpretation

The results indicate that:

* band gap depends on multiple interacting factors
* structural descriptors are highly important
* non-linear feature engineering improves model understanding
* model comparison is necessary because more complex models do not always perform better

## Tools and Libraries

* Python
* Pandas
* NumPy
* Scikit-learn
* Matplotlib
* Google Colab

## Status

Completed first-stage modeling, feature engineering, explainable AI analysis, and model comparison.

## Future Work

* improve visualizations
* test additional descriptors
* apply the workflow to XRD and SEM-based materials analysis
