# Predictive Maintenance Using CRISP-DM

## Project Overview

This project applies the CRISP-DM (Cross-Industry Standard Process for Data Mining) methodology to machine failure classification.

The goal is to use machine operating data to identify possible failure conditions and support maintenance inspection decisions.

The project uses the **AI4I 2020 Predictive Maintenance Dataset** from the UCI Machine Learning Repository. The dataset contains synthetic industrial machine data.

Two machine learning models, Logistic Regression and Random Forest, are compared. The project also explores class imbalance, classification thresholds, model evaluation, and a simple prediction demonstration.

## 1. Business Understanding

### Project Background

Unexpected machine failures can interrupt manufacturing operations and increase maintenance costs.

Machine learning can help identify operating conditions associated with failures and support decisions about which machines may need further inspection.

### Business Objective

Support maintenance decisions by identifying machines that may be experiencing failure conditions.

### Data Mining Objective

Build a binary classification model to classify machine failure states based on operating conditions.

The model classifies failure conditions in the available dataset. It does not predict when a machine will fail or estimate its remaining useful life.

### Success Criteria

- Compare classification models using appropriate performance metrics.
- Evaluate precision, recall, F1-score, ROC-AUC, and confusion matrices.
- Consider class imbalance and the trade-off between missed failures and false alarms.
- Select a classification threshold using training cross-validation results.
- Provide a reproducible Colab notebook and a simple prediction demonstration.
- Document model limitations and potential maintenance considerations.

## 2. Data Understanding

### Dataset

- **Name:** AI4I 2020 Predictive Maintenance Dataset
- **Source:** UCI Machine Learning Repository
- **Dataset type:** Synthetic industrial machine data
- **Records:** 10,000
- **Columns:** 14
- **Target:** `Machine failure`

Dataset link: https://archive.ics.uci.edu/dataset/601/ai4i+2020+predictive+maintenance+dataset

The dataset contains operating measurements, product information, and machine failure indicators.

### Key Findings

The dataset contains:

- 9,661 normal records (96.61%).
- 339 failure records (3.39%).
- No missing values.
- No duplicate operating records after excluding identifiers.

The target is highly imbalanced, with relatively few failure examples.

Exploratory data analysis included:

- Target class distribution.
- Product type failure rates.
- Sensor distributions and boxplots.
- Correlation analysis.
- Mean and median comparisons between normal and failed machines.

The failure group showed higher average torque and tool wear, along with lower median rotational speed.

These observations describe relationships in the synthetic dataset and do not establish the causes of machine failures.

## 3. Data Preparation

The dataset was divided into training and testing sets using an 80/20 stratified split with `random_state=42`.

| Dataset | Records | Failure Cases |
|---|---:|---:|
| Training | 8,000 | 271 |
| Testing | 2,000 | 68 |

### Feature Selection

The model uses six original input features:

- Air temperature [K]
- Process temperature [K]
- Rotational speed [rpm]
- Torque [Nm]
- Tool wear [min]
- Type

The following columns were excluded:

- `UDI` and `Product ID`: identifiers.
- `TWF`, `HDF`, `PWF`, `OSF`, and `RNF`: failure-type indicators that could reveal information about the target.
- `Machine failure`: target variable.

Excluding the failure-type indicators helps prevent target leakage.

### Preprocessing

The preprocessing pipeline includes:

- Median imputation for numeric features.
- StandardScaler for numeric features.
- Most-frequent imputation for categorical features.
- One-hot encoding for product type.

Preprocessing is included inside the machine learning pipelines so that the rules are fitted using the training data.

After transformation, the data contains eight features.

Additional validation confirmed that the transformed training and testing data contained no NaN or infinite values.

## 4. Modeling

Two classification models were trained and compared:

1. Logistic Regression
2. Random Forest

Both models used class weighting to address the imbalanced target distribution.

Model performance was examined using five-fold stratified cross-validation.

### Classification Threshold

Because machine failures are uncommon, the default classification threshold of 0.5 may not provide the best balance between precision and recall.

Random Forest thresholds were compared using out-of-fold predictions from the training data.

A threshold of **0.2** achieved the highest F1-score among the tested thresholds and was selected for the demonstration.

This threshold was selected using training cross-validation results, not the held-out test set.

It is not necessarily the best threshold for real maintenance operations because actual maintenance costs and acceptable false-alarm rates are unknown.

### Feature Importance

Permutation importance was used to examine how much the trained Random Forest relied on each input feature.

Torque and rotational speed had the highest importance values in the training-data analysis.

However, feature importance does not establish causation. Correlations between input features and the choice of evaluation metric can also affect the results.

## 5. Evaluation

The trained models were evaluated on the held-out test set containing 2,000 records.

### Model Performance

| Model | Precision | Recall | F1-score | ROC-AUC |
|---|---:|---:|---:|---:|
| Logistic Regression (0.5) | 0.1418 | 0.8235 | 0.2419 | 0.9070 |
| Random Forest (0.5) | 0.9091 | 0.4412 | 0.5941 | 0.9636 |
| **Random Forest (0.2)** | **0.5699** | **0.7794** | **0.6584** | **0.9636** |

Random Forest with the selected threshold of 0.2 achieved the highest F1-score among the evaluated configurations.

### Confusion Matrix Results

For Random Forest with threshold 0.2:

- True positives: 53
- False negatives: 15
- False positives: 40
- True negatives: 1,892

The model detected **53 out of 68 actual failures** in the test set.

Lowering the threshold increased failure detection but also produced more false alarms compared with the default threshold of 0.5.

### Naive Baseline Comparison

An always-normal baseline was evaluated to demonstrate why accuracy alone can be misleading for imbalanced data.

| Metric | Always Normal | Random Forest (0.2) |
|---|---:|---:|
| Accuracy | 0.9660 | 0.9725 |
| Precision | 0.0000 | 0.5699 |
| Recall | 0.0000 | 0.7794 |
| F1-score | 0.0000 | 0.6584 |

The always-normal baseline achieved 96.60% accuracy but failed to detect any machine failures.

The comparison shows why recall and F1-score are important for this classification task.

## 6. Deployment

A simple deployment demonstration was implemented in Google Colab.

The demonstration includes:

- Saving the trained Random Forest pipeline.
- Loading the saved pipeline.
- Generating predictions for a new example.
- Applying the selected classification threshold of 0.2.
- Validating input data before prediction.

### Model Persistence

The original and reloaded pipelines were compared using 2,000 test samples.

**Result: PASS**

The prediction scores were consistent after saving and loading the model in the tested environment.

### Example Prediction

An example machine record produced:

- Failure score: 0.0050
- Classification threshold: 0.2
- Prediction: NORMAL

The failure score is a model output and is not a calibrated real-world failure probability.

### Input Validation

Basic input validation checks:

- Required input columns.
- Finite numeric values.
- Valid product types (L, M, H).
- Non-negative tool wear time.

The validation tests confirmed that a valid example was accepted and an example with negative tool wear was rejected.

The validation function was also integrated into the demonstration prediction function.

This is an educational prototype, not a production-ready industrial monitoring system.

## 7. AI-Assisted Expert Review and Revisions

Each CRISP-DM phase was reviewed and improved through an iterative AI-assisted review process.

The process included:

1. Initial AI critique of the phase.
2. First implementation revision.
3. Second AI review.
4. Second implementation revision.
5. Third AI review.

The review process examined methodological decisions, evaluation results, documentation quality, and remaining limitations.

A total of six CRISP-DM phases were reviewed, with three AI review rounds and two implementation revisions documented for each phase.

The review log records the review prompts, findings, changes, and remaining limitations.

See [AI Expert Review Log](ai_review_log.md) for details.

These reviews were AI-assisted critiques rather than independent assessments by human domain experts.

## 8. How to Run

The project was developed using Python in Google Colab.

### Steps

1. Open `CRISP_DM_Predictive_Maintenance.ipynb` in Google Colab.
2. Select **Runtime → Run all**.
3. Review the outputs for each CRISP-DM phase.

The dataset is automatically downloaded from the UCI Machine Learning Repository.

Manual CSV upload is not required.

The notebook includes data exploration, preprocessing, model training, evaluation, model persistence, and prediction demonstrations.

An internet connection is required to download the dataset.

## 9. Tools and Libraries

- Python
- Google Colab
- pandas
- NumPy
- Matplotlib
- Seaborn
- scikit-learn
- joblib

## 10. Project Files

| File | Description |
|---|---|
| `CRISP_DM_Predictive_Maintenance.ipynb` | Complete Colab notebook covering all six CRISP-DM phases |
| `ai_review_log.md` | AI-assisted critique and revision history |
| `README.md` | Project overview, methodology, results, and instructions |

## 11. Limitations

This project has several limitations:

- The dataset is synthetic and does not represent a specific manufacturing facility.
- The classification model identifies failure states rather than predicting future failure times.
- Random train/test splitting does not validate performance across different machines, factories, or time periods.
- The selected threshold was based on F1-score rather than actual maintenance costs.
- Model scores were not calibrated as real-world failure probabilities.
- The input validation demonstration does not cover all possible invalid values or sensor operating ranges.
- The model has not been tested or deployed on real industrial equipment.

Additional validation, operational requirements, and monitoring would be necessary before real-world deployment.

## 12. Conclusion

This project demonstrated how the six CRISP-DM phases can be applied to a machine failure classification problem.

Random Forest with a threshold of 0.2 achieved an F1-score of 0.6584 and recall of 0.7794 on the held-out test set.

The project also demonstrated the importance of handling class imbalance, preventing target leakage, comparing classification thresholds, and validating model inputs.

Although the model is not ready for industrial deployment, the project provides a reproducible educational example of a complete data mining workflow.
