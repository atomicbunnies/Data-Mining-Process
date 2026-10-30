# AI-Assisted Review and Revision Log

**Project:** Predicting Overwatch League Match Winners Using SEMMA  
**Methodology:** SEMMA (Sample, Explore, Modify, Model, Assess)  
**Dataset:** Overwatch League with Odds (Kaggle)  
**Review Process:** Three AI review rounds and two revisions per phase

## Purpose

This document records how the project was improved through AI-assisted reviews and revisions.

ChatGPT acted as a data mining expert by critically reviewing each SEMMA phase, identifying weaknesses, and recommending improvements. The recommendations were evaluated and applied to the Google Colab notebook.

These were AI-assisted critiques, not independent reviews by external experts.

Each phase followed this process:

**Initial implementation → Review 1 → Revision 1 → Review 2 → Revision 2 → Review 3**

The review descriptions below summarize the actual review discussions and changes rather than reproducing every conversation prompt verbatim.

---

# 1. Sample

## Initial Implementation

The Sample phase loaded the `owl-with-odds.csv` dataset and examined its structure.

The initial notebook included:

- Dataset shape, columns, and data types.
- Missing-value inspection.
- Match dates and participating teams.
- Target distribution.
- Winner-label and match-ID validation.

The dataset contained **715 matches, 58 original columns, and 20 teams**, covering January 2018 through June 2020.

The target was `winner_label`, where 0 represented a Team 1 win and 1 represented a Team 2 win.

There were 339 Team 1 wins and 376 Team 2 wins.

All 715 records were initially retained.

## AI Expert Review — Round 1

**Review focus:** Dataset suitability, sampling decisions, target distribution, missing values, dates, and basic data quality.

**Critique:**

The initial implementation provided an overview of the dataset, but several decisions needed clearer justification.

The notebook did not sufficiently explain why all records were retained. Missing-value counts also needed a more detailed summary.

One match had an invalid or missing date, which could affect chronological splitting later.

The review also identified the need to check whether a match incorrectly listed the same team on both sides.

**Recommendations:**

1. Explain the decision to retain all available records.
2. Add detailed missing-value counts and percentages.
3. Identify the record with an invalid or missing date.
4. Check for identical teams within a match.
5. Document which issues would be handled during Modify.

## Revision 1

**Changes implemented:**

The Sample Findings Markdown was revised to explain why all 715 matches were retained during initial analysis.

Code `[6]` was added to provide more detailed data quality checks.

The additional analysis examined missing-value percentages, unique teams, invalid dates, and matches with identical participating teams.

The notebook also documented the plan to handle the missing date and missing historical statistics during Modify rather than removing records prematurely.

**Observed results:**

- One match had an invalid or missing date.
- No matches listed the same team on both sides.
- Missing values were present in several historical performance columns.
- All 715 records were retained during Sample.

**Improvement:**

The dataset selection and data quality decisions became clearer and easier to reproduce.

## AI Expert Review — Round 2

**Review focus:** Reassessment of the expanded quality checks and identification of remaining data consistency risks.

**Critique:**

The first revision addressed the earlier documentation and inspection problems.

However, the notebook had not yet verified whether historical team statistics were internally consistent.

For example, a team's wins plus losses should agree with its recorded match count, and win-rate values should fall within the expected range.

**Recommendation:**

Add checks for season and all-time match-count consistency and valid win-percentage ranges for both teams.

## Revision 2

**Changes implemented:**

Code `[7]` was added to check:

- Team 1 season wins + losses versus season matches.
- Team 1 all-time wins + losses versus all-time matches.
- Team 2 season wins + losses versus season matches.
- Team 2 all-time wins + losses versus all-time matches.
- Valid ranges for season and all-time win percentages.

**Observed results:**

All four match-count consistency checks reported zero inconsistent rows.

All four checked win-percentage columns reported zero invalid values.

**Validation result:** PASS

**Improvement:**

The Sample phase now included explicit checks of historical statistical consistency rather than relying only on basic data inspection.

## AI Expert Review — Round 3

**Final review:**

The Sample phase now documented dataset selection, target balance, missing values, date issues, match identifiers, and historical statistical consistency.

The checks supported proceeding with the dataset.

**Remaining limitations:**

The dataset was relatively small, some historical features had missing values, and one match had no valid date.

The consistency checks did not independently prove that every historical statistic had been calculated using only information available before its match.

**Decision:** Sample completed. Proceed to Explore.

---

# 2. Explore

## Initial Implementation

The Explore phase examined:

- Match distribution by year.
- Team-level match counts and overall win rates.
- Recent five-match performance differences and match outcomes.
- Missing values in historical performance features.

These analyses were implemented in Code `[8]` through `[11]`.

**Initial observations:**

- 2018: 262 dated matches.
- 2019: 318 dated matches.
- 2020: 134 dated matches.
- One record was excluded from yearly counts because its date was invalid or missing.

New York Excelsior had an overall observed win rate of 75.3%, followed by Vancouver Titans at 72.0%.

The exploratory analysis suggested a relationship between recent team form and actual match results.

## AI Expert Review — Round 1

**Review focus:** Quality of exploratory analysis, interpretation, feature relevance, missing values, and potential leakage.

**Critique:**

The initial Explore Findings section mainly described which analyses had been performed.

It did not sufficiently explain the actual numerical patterns shown by the tables and graphs.

The association between recent form and match outcomes was particularly important and needed explicit interpretation.

The review also emphasized that overall team win rates calculated from the complete dataset could not safely be used as pre-match prediction features.

**Recommendation:**

Replace the descriptive findings with a clearer discussion of actual results, limitations, and implications for Modify.

## Revision 1

**Changes implemented:**

The Explore Findings Markdown was rewritten to include numerical results and interpretations.

The revised findings documented the recent-form analysis:

- When Team 1 had better recent form, Team 1 won 73.2% of matches.
- When both teams had similar recent form, Team 1 won 45.6%.
- When Team 2 had better recent form, Team 1 won 25.8%.

The notebook also explained the missing-value patterns.

Recent 20-match win-rate features had approximately 28% missing values, while recent five-match features had approximately 7%.

The revised findings explicitly distinguished exploratory associations from validated predictive performance.

**Improvement:**

The Explore phase now explained what the results meant instead of merely listing the analyses.

## AI Expert Review — Round 2

**Review focus:** Strength of the recent-form finding and whether it was consistent across seasons.

**Critique:**

The improved findings showed a relationship between recent team performance and match outcomes.

However, the results had only been examined across the combined dataset.

It remained unclear whether the same pattern appeared separately in 2018, 2019, and 2020.

**Recommendation:**

Compare recent-form groups by year and include both win rates and group sizes.

## Revision 2

**Changes implemented:**

Code `[12]` was added to calculate Team 1 win rates by year and recent-form group.

The code used the difference between the two teams' recent five-match win rates, grouped matches into three categories, and produced a yearly comparison table and grouped bar chart.

**Observed results:**

When Team 1 had better recent form, Team 1's win rates were:

- 2018: 73.1%.
- 2019: 74.3%.
- 2020: 71.1%.

When Team 2 had better recent form, Team 1's win rates were:

- 2018: 19.1%.
- 2019: 27.8%.
- 2020: 31.8%.

The Findings Markdown was expanded to explain these year-by-year observations.

**Improvement:**

The association between recent form and match outcomes was observed across all three years rather than only in the combined dataset.

## AI Expert Review — Round 3

**Final review:**

The Explore phase covered general match patterns, descriptive team performance, recent-form relationships, missing values, and year-by-year comparisons.

The analyses were appropriate for understanding the dataset before modeling.

**Remaining limitations:**

- Some yearly groups contained relatively few matches.
- Associations did not establish causation.
- Descriptive overall team win rates could contain future match outcomes.
- The pre-match timing of supplied historical statistics was not independently verified.

**Decision:** Explore completed. Proceed to Modify.

---

# 3. Modify

## Initial Implementation

The Modify phase prepared the data for machine learning.

The initial implementation:

1. Excluded the record without a valid match date.
2. Sorted the remaining matches chronologically.
3. Created five comparison features using differences between Team 1 and Team 2.
4. Split the data into training and test sets using match dates.
5. Prepared median imputation and standard scaling.

**Modeling records:** 714

**Selected features:**

- `season_win_rate_diff`
- `alltime_win_rate_diff`
- `recent_5_win_rate_diff`
- `season_matches_diff`
- `alltime_matches_diff`

The chronological split produced:

- Training set: 561 matches.
- Test set: 153 matches.

The target and betting odds were excluded from model predictors.

## AI Expert Review — Round 1

**Review focus:** Feature selection, missing-value treatment, chronological splitting, preprocessing, and leakage prevention.

**Critique:**

The original preprocessing implementation correctly fitted its parameters on the training set.

However, preprocessing had been executed separately from the model-fitting workflow.

For future cross-validation, preprocessing should be included inside the model pipeline so that each training fold learns its own imputation and scaling parameters.

**Recommendation:**

Create a reusable pipeline that combines preprocessing with a classification model.

## Revision 1

**Changes implemented:**

Code `[17]` was added to create a reusable pipeline containing:

1. A cloned preprocessing pipeline.
2. Logistic Regression as the classification step.

The structure was validated without training the model at this stage.

The Findings Markdown was updated to explain how the pipeline would support safer cross-validation.

**Improvement:**

The modeling structure became better suited to fold-specific preprocessing and reduced the risk of preprocessing leakage.

## AI Expert Review — Round 2

**Review focus:** Verification of selected features and chronological train/test separation.

**Critique:**

The reusable pipeline improved the preprocessing design.

However, the notebook needed a more explicit final check that the chosen input columns excluded the target and betting odds.

It was also useful to verify that the training and test sets contained both target classes, used identical feature columns, and had non-overlapping date ranges.

**Recommendation:**

Add a final validation cell that checks feature safety and the train/test split.

## Revision 2

**Changes implemented:**

Code `[18]` was added to verify:

- No target or betting odds columns in the selected features.
- Identical feature columns in training and test sets.
- Chronological separation between training and test dates.
- Both winner labels present in both datasets.
- Training/test sizes and class distributions.

**Observed result:** Final Modify validation passed.

The Findings Markdown was updated to document these checks.

**Improvement:**

The training data structure and split assumptions were now explicitly validated before modeling.

## AI Expert Review — Round 3

**Final review:**

The Modify phase prepared five interpretable comparison features, addressed missing values through a pipeline, and separated earlier matches from later matches.

The final validation checks supported proceeding with modeling.

**Remaining limitations:**

- The source dataset's historical features were not independently reconstructed.
- The available sample was relatively small.
- Median imputation was a practical choice but could not recover the true missing historical statistics.

**Decision:** Modify completed. Proceed to Model.

---

# 4. Model

## Initial Implementation

The Model phase compared two classification models:

- Logistic Regression.
- Random Forest.

Both models used the same five comparison features and preprocessing structure.

Initial five-fold `TimeSeriesSplit` cross-validation was performed on the training data.

**Initial cross-validation results:**

| Model | Accuracy Mean | F1 Mean |
|---|---:|---:|
| Logistic Regression | 0.6452 | 0.6749 |
| Random Forest | 0.6086 | 0.6091 |

Logistic Regression achieved the higher mean F1-score and was initially selected.

The selected model was trained on the full training set.

## AI Expert Review — Round 1

**Review focus:** Model comparison, cross-validation, model selection, and chronological evaluation.

**Critique:**

Both models were compared under the same general conditions, and preprocessing was correctly included in the model pipelines.

However, the original `TimeSeriesSplit` divided observations by row position rather than by unique match date.

Matches played on the same date could therefore appear in both the training and validation portions of a fold.

**Recommendation:**

Check the date boundaries of all five cross-validation folds before relying on the model comparison.

## Revision 1

**Changes implemented:**

Code `[22]` was added to inspect the last training date and first validation date in each fold.

**Observed results:**

- Fold 1: Shared date.
- Fold 2: Shared date.
- Fold 3: No shared date.
- Fold 4: Shared date.
- Fold 5: Shared date.

Four of five folds shared a date across their training and validation boundaries.

**Improvement:**

The notebook identified a concrete weakness in its evaluation design rather than assuming that sorted rows guaranteed date-separated folds.

This overlap did not by itself prove that target leakage occurred, but it justified a stricter chronological split.

## AI Expert Review — Round 2

**Review focus:** Cross-validation date overlap and its implications for evaluation reliability.

**Critique:**

The date-boundary validation confirmed that the original row-based splitting strategy did not consistently separate complete match dates.

For a stricter chronological evaluation, all matches on the same date should stay within the same training or validation group.

**Recommendation:**

Rebuild cross-validation folds using unique match dates, re-evaluate both models, and select the final model again using the revised results.

## Revision 2

**Changes implemented:**

Code `[23]` was added to construct date-safe cross-validation folds.

The updated implementation:

1. Extracted unique training match dates.
2. Applied five-fold `TimeSeriesSplit` to those dates.
3. Mapped the selected dates back to match records.
4. Verified that each training fold ended before its validation fold began.
5. Re-evaluated both classification pipelines.

Code `[24]` then selected the model again using the revised mean F1-score and fitted it on the full training set.

**Revised cross-validation results:**

| Model | Accuracy Mean | Accuracy Std | F1 Mean | F1 Std |
|---|---:|---:|---:|---:|
| Logistic Regression | 0.6629 | 0.0721 | 0.6891 | 0.0599 |
| Random Forest | 0.6207 | 0.0269 | 0.6330 | 0.0240 |

**Validation:** Five date-safe folds; no overlapping training/validation dates.

Logistic Regression remained the better model according to mean F1-score.

**Final selected model:** Logistic Regression.

The Model Findings Markdown was expanded to document the original problem and the corrected cross-validation process.

## AI Expert Review — Round 3

**Final review:**

The final implementation compared the models on consistent features and used pipelines to keep preprocessing within each training fold.

The detected date-overlap issue had been addressed, and both models were evaluated again under the revised setup.

Logistic Regression retained the higher mean cross-validation F1-score.

**Remaining limitations:**

- Cross-validation performance was not the same as held-out test performance.
- Only two models and a limited feature set were compared.
- The source historical statistics were not independently reconstructed.

**Decision:** Model completed. Proceed to Assess with the revised Logistic Regression pipeline.

---

# 5. Assess

## Initial Implementation

The Assess phase evaluated the final Logistic Regression model on 153 held-out test matches.

The initial assessment included:

- Accuracy, Precision, Recall, and F1-score.
- A classification report.
- A confusion matrix.
- Comparison with a majority-class baseline.

**Held-out test results:**

| Metric | Logistic Regression |
|---|---:|
| Accuracy | 0.6536 |
| Precision (Team 2 wins) | 0.6629 |
| Recall (Team 2 wins) | 0.7195 |
| F1-score (Team 2 wins) | 0.6901 |

The model correctly predicted 100 of 153 matches.

**Initial majority-baseline comparison:**

| Model | Accuracy | F1-score |
|---|---:|---:|
| Majority Baseline | 0.5359 | 0.6979 |
| Logistic Regression | 0.6536 | 0.6901 |

The model achieved higher accuracy, but the baseline had a slightly higher F1-score for Team 2 wins.

## AI Expert Review — Round 1

**Review focus:** Final test metrics, confusion matrix, baseline comparison, and interpretation of the positive class.

**Critique:**

The initial evaluation was technically valid, but the reported binary F1-score focused on Team 2 wins because Team 2 was encoded as class 1.

The majority baseline always predicted Team 2 wins.

This allowed the baseline to achieve high recall for Team 2 while completely failing to predict Team 1 wins.

As a result, single-class F1-score alone did not adequately summarize performance across both possible winners.

**Recommendation:**

Add Macro F1 and Balanced Accuracy so that Team 1 and Team 2 receive equal consideration.

## Revision 1

**Changes implemented:**

Code `[28]` was added to calculate Macro F1 and Balanced Accuracy for both Logistic Regression and the majority baseline.

**Observed results:**

| Model | Accuracy | Macro F1 | Balanced Accuracy |
|---|---:|---:|---:|
| Majority Baseline | 0.5359 | 0.3489 | 0.5000 |
| Logistic Regression | 0.6536 | 0.6487 | 0.6485 |

The Assess Findings Markdown was updated to explain why these additional metrics were needed.

**Improvement:**

The revised evaluation showed that Logistic Regression performed better than the majority baseline when both winner classes were considered equally.

It also preserved the important finding that the baseline's Team 2 F1-score was slightly higher.

## AI Expert Review — Round 2

**Review focus:** Interpretation of classification errors and the practical significance of the final test performance.

**Critique:**

The additional metrics improved the evaluation.

However, the written findings still did not clearly explain the types of mistakes made by the selected model.

The confusion matrix provided this information, but its implications needed to be documented.

**Recommendation:**

Expand the Findings section with the confusion matrix results and explain the model's remaining errors and limitations.

## Revision 2

**Changes implemented:**

The Findings Markdown was expanded with a dedicated **Prediction Errors and Final Interpretation** section.

**Observed confusion matrix results:**

- Correctly predicted Team 1 wins: 41.
- Correctly predicted Team 2 wins: 59.
- Team 1 wins incorrectly predicted as Team 2: 30.
- Team 2 wins incorrectly predicted as Team 1: 23.

**Total correct predictions:** 100  
**Total incorrect predictions:** 53

The revised interpretation explained that Logistic Regression performed better than the majority baseline on Accuracy, Macro F1, and Balanced Accuracy.

It also noted that 53 incorrect predictions out of 153 matches represented a meaningful limitation.

**Improvement:**

The assessment now reported not only numerical scores but also how the model made mistakes and what those errors meant.

## AI Expert Review — Round 3

**Final review:**

The Assess phase included the necessary held-out evaluation metrics, a confusion matrix, a majority-class comparison, and additional metrics for evaluating both winner classes fairly.

The evaluation also documented the selected model's errors and dataset limitations.

**Remaining limitations:**

- Test Accuracy was approximately 65%, leaving substantial prediction errors.
- The test set contained only 153 matches.
- The historical data covered 2018–2020 and might not represent newer competitive conditions.
- Information such as roster changes, team strategy, and match-day conditions was not modeled.
- The pre-match availability of supplied historical statistics was not independently verified.

**Decision:** Assess completed. The model demonstrated useful predictive performance relative to a simple majority baseline on the reported balanced metrics, but the results did not establish reliable performance for real-world forecasting.

---

# 6. Final Project Summary

The project completed all five SEMMA phases.

| Phase | AI Reviews | Revisions | Final Status |
|---|---:|---:|---|
| Sample | 3 | 2 | Completed |
| Explore | 3 | 2 | Completed |
| Modify | 3 | 2 | Completed |
| Model | 3 | 2 | Completed |
| Assess | 3 | 2 | Completed |
| **Total** | **15** | **10** | **Completed** |

## Major Improvements From AI-Assisted Review

The reviews led to five important improvements:

1. **Sample:** Added more detailed data quality and consistency checks.
2. **Explore:** Replaced general descriptions with numerical findings and added year-by-year analysis.
3. **Modify:** Created a reusable preprocessing/model pipeline and added explicit feature and chronological split validation.
4. **Model:** Detected same-date overlap in the original cross-validation folds and replaced them with date-safe folds.
5. **Assess:** Added Macro F1 and Balanced Accuracy after discovering that a majority baseline could achieve a high class-specific F1-score without predicting both teams.

## Final Outcome

Logistic Regression was selected based on the revised date-safe cross-validation results.

On the 153-match held-out test set, it achieved:

- **Accuracy:** 0.6536
- **Macro F1:** 0.6487
- **Balanced Accuracy:** 0.6485
- **Correct predictions:** 100
- **Incorrect predictions:** 53

Compared with the majority-class baseline, the selected model performed better in predicting both winner classes.

The project demonstrated how repeated review can improve data validation, evaluation design, and interpretation—not merely produce a higher score.

The final conclusions remain limited by the historical dataset, the modest sample size, and the unverified pre-match timing of the supplied statistical features.

**Final status:** SEMMA implementation, AI-assisted reviews, and revisions completed.
