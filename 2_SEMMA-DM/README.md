# Predicting Overwatch League Match Winners Using SEMMA

**CMPE 255 - Data Mining | Data Mining Process Project 2**

This project applies the **SEMMA** methodology—**Sample, Explore, Modify, Model, and Assess**—to historical Overwatch League match data. The objective is to predict which of two teams wins a match based on the teams' historical performance statistics.

The project compares Logistic Regression and Random Forest, evaluates them using chronologically ordered cross-validation, and tests the selected model on later matches that were not used for model selection.

> **Scope:** This is an educational analysis of matches from 2018–2020, not a prediction system for current Overwatch competition. Although the source fields describe historical statistics, their availability strictly before each match was not independently verified. The results should therefore not be interpreted as validated real-world pre-match forecasting performance.

## Dataset

- **Source:** [Overwatch League Head-to-Head Statistics with Odds (Kaggle)](https://www.kaggle.com/datasets/mdabbert/overwatch-league-with-odds)
- **Input file:** `owl-with-odds.csv`
- **Original dataset:** 715 matches, 58 columns, 20 teams
- **Years covered:** 2018–2020
- **Modeling dataset:** 714 matches after excluding one record without a valid match date
- **Target:** `winner_label` (`0` = Team 1 wins; `1` = Team 2 wins)

The original dataset has 339 Team 1 wins and 376 Team 2 wins. Betting odds and outcome information were excluded from the model inputs.

## SEMMA Workflow

### 1. Sample

Loaded and inspected the dataset, reviewed missing values and target balance, and checked match identifiers and team-statistics consistency. One invalid or missing match date was identified for exclusion before chronological modeling.

### 2. Explore

Examined match volume by year, team-level descriptive results, historical-feature missingness, and the relationship between recent five-match form and actual outcomes. When Team 1 had better recent form, Team 1 won approximately **73.2%** of the corresponding matches in the exploratory comparison. This is an association, not proof of causation or future forecasting performance.

### 3. Modify

Created five team-comparison features:

| Feature | Description |
|---|---|
| `season_win_rate_diff` | Difference in season win rates |
| `alltime_win_rate_diff` | Difference in all-time win rates |
| `recent_5_win_rate_diff` | Difference in recent five-match win rates |
| `season_matches_diff` | Difference in season match counts |
| `alltime_matches_diff` | Difference in all-time match counts |

Used a **chronological, date-separated** training/test split: **561 training matches** and **153 test matches**. Missing feature values were handled using median imputation, followed by standard scaling, inside scikit-learn pipelines. The target, betting odds, and direct match outcomes were not used as predictors.

### 4. Model

Compared **Logistic Regression** with **Random Forest** using five-fold time-series cross-validation on the training portion. An initial row-based split was found to share match dates across training and validation boundaries in four folds. The evaluation was corrected by splitting **unique match dates** before mapping folds back to records, keeping all matches on a date together.

**Corrected five-fold cross-validation results:**

| Model | Accuracy (mean ± std) | F1 (mean ± std) |
|---|---:|---:|
| **Logistic Regression** | **0.6629 ± 0.0721** | **0.6891 ± 0.0599** |
| Random Forest | 0.6207 ± 0.0269 | 0.6330 ± 0.0240 |

Logistic Regression had the higher mean cross-validation F1-score and was selected as the final model. The selected pipeline was fitted on all 561 training matches before final evaluation.

### 5. Assess

Evaluated the selected Logistic Regression pipeline once on the **153 held-out later matches**.

| Metric | Test result |
|---|---:|
| Accuracy | **0.6536** |
| Precision (Team 2 wins) | 0.6629 |
| Recall (Team 2 wins) | 0.7195 |
| F1 (Team 2 wins) | 0.6901 |
| **Macro F1** | **0.6487** |
| **Balanced Accuracy** | **0.6485** |

**Confusion matrix (actual rows, predicted columns):**

| Actual / Predicted | Team 1 wins | Team 2 wins |
|---|---:|---:|
| Team 1 wins | 41 | 30 |
| Team 2 wins | 23 | 59 |

The model correctly predicted **100 of 153** matches and incorrectly predicted **53**.

**Majority-class baseline comparison:**

| Model | Accuracy | Macro F1 | Balanced Accuracy |
|---|---:|---:|---:|
| Always predict Team 2 wins | 0.5359 | 0.3489 | 0.5000 |
| **Logistic Regression** | **0.6536** | **0.6487** | **0.6485** |

The majority baseline's **Team 2-specific F1** (0.6979) was slightly higher than Logistic Regression's (0.6901), because the baseline predicts Team 2 for every match. **Macro F1 and Balanced Accuracy** provide a more informative comparison across both winner classes.

## Key Findings and Limitations

Logistic Regression outperformed Random Forest on the corrected training cross-validation and performed better than a simple majority-class baseline on test accuracy, Macro F1, and Balanced Accuracy. However, its **65.36% test accuracy** still represents substantial prediction error.

Limitations include the relatively small held-out test set (153 matches), data from only 2018–2020, unmodeled factors such as roster changes and match-day circumstances, and the unverified pre-match timing of the provided historical-statistics columns. This analysis does **not** demonstrate production-ready forecasting performance.

## How to Run

1. Download `owl-with-odds.csv` from the [Kaggle dataset page](https://www.kaggle.com/datasets/mdabbert/overwatch-league-with-odds).
2. Open `SEMMA_Overwatch_Esports_Analysis.ipynb` in **Google Colab**.
3. Run the notebook from top to bottom. When prompted by `files.upload()`, upload `owl-with-odds.csv`.
4. Review the results for the five SEMMA phases and the final held-out test evaluation.

The notebook uses **Python**, **pandas**, **NumPy**, **Matplotlib**, **Seaborn**, and **scikit-learn** in Google Colab. Its original and revised cross-validation cells are intentionally retained to document the identified date-overlap problem and its correction; the **revised date-safe evaluation** is the one used for final model selection.

## Project Files and Resources

- `SEMMA_Overwatch_Esports_Analysis.ipynb` — Full notebook with code, outputs, and SEMMA findings.
- `ai_review_log.md` — AI-assisted expert review history, including three reviews and two revisions for each of the five SEMMA phases.
- `README.md` — Project overview and instructions (this file).

**Medium article:** To be added after publication.  
**YouTube walkthrough:** To be added after upload.

## AI-Assisted Review Process

Each SEMMA phase received **three AI-assisted review rounds and two revisions**. Reviews prompted additional dataset consistency checks, stronger exploration findings, preprocessing validation, corrected date-safe cross-validation, and a more balanced test evaluation. The complete review and revision record is documented in `ai_review_log.md`.

## Conclusion

This project illustrates an end-to-end SEMMA workflow for a historical esports classification problem. Its most important lesson is that **evaluation design and interpretation matter as much as model choice**: fixing the cross-validation date boundaries and evaluating both winner classes gave a more credible view of what the model could—and could not—predict.
