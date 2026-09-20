Explainable Patient Readmission Prediction

1. Project Overview

This project aims to predict whether a patient will be readmitted to the hospital within 30 days.

The project uses the Diabetes 130-US Hospitals dataset and focuses on binary classification and model explainability.

1 = readmitted within 30 days (<30)

0 = not readmitted within 30 days (>30 or NO)

2. Data Quality Analysis

The dataset contains 101,766 encounters and 50 columns.

Several data quality issues were identified:

Some missing values are stored as NaN.

Some missing values are encoded as ?.

weight, medical_specialty, and payer_code have very high missing rates and were removed.

patient_nbr is repeated across multiple encounters.

encounter_id is unique for each encounter.

Diagnosis and administrative ID variables contain many categorical values.

3. Data Preprocessing

The target variable was converted into a binary variable called early_readmission.

Patient-level splitting was used to avoid having the same patient in both training and test sets.

The identifier columns patient_nbr and encounter_id were removed from the model features.

Categorical variables were handled using One-Hot Encoding. Administrative ID variables such as admission_type_id, discharge_disposition_id, and admission_source_id were treated as categorical features.

Missing categorical values were handled using training-set modes or a separate Missing category for laboratory results.

4. Class Imbalance

The positive class (early_readmission = 1) is the minority class.

Class imbalance was handled using:

class_weight="balanced" for Logistic Regression

scale_pos_weight for XGBoost

PR-AUC was also used as an important evaluation metric because the target is imbalanced.

5. Models

Two models were developed:

Logistic Regression

Logistic Regression was used as the baseline model.

Test results:

ROC-AUC: 0.659

PR-AUC: 0.203

Positive-class precision: 0.17

Positive-class recall: 0.55

Positive-class F1: 0.25

XGBoost

XGBoost was tuned using RandomizedSearchCV with patient-level GroupKFold cross-validation.

The selected parameters were:

n_estimators: 200

max_depth: 5

learning_rate: 0.05

subsample: 0.8

colsample_bytree: 0.8

scale_pos_weight: 5

Test results:

ROC-AUC: 0.675

PR-AUC: 0.213

Positive-class precision: 0.24

Positive-class recall: 0.30

Positive-class F1: 0.26

6. Threshold Analysis

Different probability thresholds were tested to study the precision-recall trade-off.

For example, lowering the threshold from 0.5 to 0.2 increased recall substantially, but also reduced precision.

This shows that the classification threshold should depend on the practical goal of the prediction system.

7. SHAP Explainability

SHAP was used to explain the XGBoost model.

The analysis included:

Global SHAP summary plot

SHAP feature importance

Local SHAP waterfall plot

False negative analysis

number_inpatient was one of the most influential features in the model.

SHAP explains how features affect model predictions, but high SHAP importance does not mean that a feature is a clinical cause of readmission.

8. Critical Audit

Some discharge_disposition_id features showed high SHAP importance.

Because discharge disposition is known at discharge, it may create temporal leakage if the model is intended to make predictions at admission or during the early stage of hospitalization.

Therefore, feature availability at the prediction timepoint should be considered before using the model in a real clinical setting.

9. Limitations

This project has several limitations:

The dataset contains substantial missing and coded values.

Some features may not be available at the intended prediction time.

SHAP explains model behavior but does not establish causality.

10. Conclusion

This project demonstrates an end-to-end workflow for patient readmission prediction, including data quality analysis, patient-level splitting, preprocessing, class imbalance handling, model tuning, evaluation, threshold analysis, and explainable AI with SHAP.
