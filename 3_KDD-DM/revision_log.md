# AI Expert Review Log — KDD YouTube Trending Analysis

**Project:** What Makes YouTube Videos Take Off? — US and KR Trending Videos (2021–2023)  
**Notebook:** `KDD_YouTube_Content_Analysis.ipynb`  
**Methodology:** Knowledge Discovery in Databases (KDD)  
**Review framework:** Three AI-assisted review checkpoints and two implemented revision cycles for each of five KDD phases.  
**Evidence:** Notebook code cells [1]–[42], stored outputs, notebook Markdown, and the sequential review discussion. The final review checkpoints summarize the phase-end discussion and validations; they do not imply an additional code change.

## Review Scope and Integrity

The reviews assessed data selection, cleaning, feature construction, evaluation methodology, content accessibility, interpretation, and reproducibility. The initial implementation was retained in the notebook so revisions can be compared with earlier results. A review is not the same thing as independent human validation or proof of causal relationships.

**Important version discrepancy discovered during final logging:** The latest uploaded notebook's saved outputs for [31], [39], and [42] report **14 successfully retrieved transcripts among 17 attempted**, whereas its Markdown at **Transcript Pilot Result**, **Interpretation - Initial Findings**, and **Interpretation - Final Expert Review**, as well as the current README, still describes an earlier run with **0 successful transcripts**. The 14 successful retrievals were **not subsequently analyzed for storytelling or introductions** in this notebook. The current document reports both run states explicitly; the notebook narrative and README must be reconciled before final submission. A successful transcript fetch by itself is not content analysis.

---

## Phase 1 — Selection

**Initial implementation [1]–[6].** Loaded US/KR Trending records and category mappings, parsed publication and Trending timestamps, inspected duplicate country/video/day records, and examined year coverage. Earlier raw timestamp comparisons produced negative Trending delays despite apparent calendar-day consistency.

### Expert Review Round 1 — Timestamp validity and repeated records
**Finding:** Day-level Trending dates should not be interpreted as exact timestamps of when videos first entered Trending. Multiple records for the same video and day also require explicit handling.  
**Revision 1 implemented — [7]:** Recalculated delay using normalized calendar dates; diagnosed repeated groups with differing view counts.  
**Evidence:** Negative calendar-day delays: **0**. Duplicate groups containing differing view counts: **3,500 KR** and **3,875 US**. A 2021–2023 candidate population of **60,925** country/video records was identified.  
**Decision:** Use calendar-day differences and explicitly choose a duplicate-resolution rule.

### Expert Review Round 2 — Reproducible selection rule
**Finding:** The analysis needed one consistent record per country/video/Trending day and one first-observed row per country/video for cross-sectional comparisons.  
**Revision 2 implemented — [8]:** Retained the highest observed view count per duplicate country/video/day and built the first-observed country/video dataset for the selected years.  
**Evidence:** **432,237** selected daily observations and **60,925** country/video observations (**38,867 US; 22,058 KR**). Four Selection checks passed.  
**Decision:** Preserve both the daily table and per-video table, and use the latter for subsequent video-level work.

### Expert Review Round 3 — Final Selection assessment
**Assessment:** Selection keys, years and nonnegative calendar delays passed validation. The sampled population represents **videos observed in Trending**, not all YouTube uploads. Calendar days to first observed Trending must not be interpreted as the exact Trending-entry time. No further Selection code revision was made after [8].

---

## Phase 2 — Preprocessing

**Initial implementation [9]–[13].** Checked missing/invalid fields, view-count distributions, tags and thumbnail URLs; reloaded descriptions from the CSV archives; removed records with invalid modeling values; and created log-transformed views.  
**Evidence:** **60,925 → 60,909** country/video records after removing **16** invalid rows. Four initial preprocessing checks passed.

### Expert Review Round 1 — Description presence versus usefulness
**Finding:** Merely having a description is not evidence of meaningful spoken or visual content. Description length and possible changes over time also matter.  
**Revision 1 implemented — [14]:** Inspected description character counts, whitespace-delimited word counts and short-description frequencies separately by country.  
**Evidence:** Median description lengths were **283 characters (KR)** and **726 (US)**. Word-count comparisons across Korean and English are crude; longer descriptions can consist of credits, promotions or URLs.  
**Decision:** Treat descriptions as imperfect metadata, not ground-truth transcripts.

### Expert Review Round 2 — Thumbnail URL feasibility
**Finding:** A populated image URL does not ensure accessible or sufficiently detailed imagery.  
**Revision 2 implemented — [15]:** Requested 10 thumbnails per country and checked response success and dimensions.  
**Evidence:** **9/10 accessible in each country**; accessible default images had median dimensions **120×90**.  
**Decision:** Test higher-resolution images and distinguish URL availability from actual image access.

### Expert Review Round 3 — Final Preprocessing assessment
**Assessment:** The cleaned dataset has valid keys and view counts. Keeping the longest available description for each video may introduce post-observation edits; it must not be claimed to represent text exactly available at publication. The thumbnail pilot justified subsequent high-resolution experiments. No third revision was implemented.

---

## Phase 3 — Transformation

**Initial implementation [16]–[20].** Constructed title, upload-time, category, tag and log-view variables; compared simple title patterns; tested high-resolution thumbnail availability; and validated transformed features.  
**Evidence:** **60,909** transformed records; **12** reported feature columns; **7/7** transformation checks passed.

### Expert Review Round 1 — One-sided title-pattern comparisons
**Finding:** Showing only videos with a question mark, exclamation mark or number provides no absent-group reference or subgroup size.  
**Revision 1 implemented — [21]:** Added **Present versus Absent** comparisons by country with subgroup counts and median first-observed Trending views.  
**Evidence:** Question marks and exclamation marks corresponded to lower median observed views than their absent groups in this exploratory comparison, while numbers had the opposite direction.  
**Decision:** Record these as noncausal associations potentially confounded by category and channel.

### Expert Review Round 2 — Missing image-derived features
**Finding:** Basic title and text features did not address thumbnail appearance despite successful high-resolution access.  
**Revision 2 implemented — [22]:** Selected 20 videos per country and extracted grayscale brightness, grayscale contrast and a simple RGB variability proxy from accessible high-resolution thumbnails.  
**Evidence:** **19/20 accessible per country; 38/40** extracted.  
**Decision:** Use these as numerical image proxies, not direct measures of attractiveness or click-through rate.

### Expert Review Round 3 — Final Transformation assessment
**Assessment:** The derived fields were valid and reproducible in the saved run; cross-language title word counts and UTC publication hour require care. The 38-image pilot established feasibility, **not** population-level relationships. A larger thumbnail sample was assigned to Data Mining. No further Transformation revision was recorded.

---

## Phase 4 — Data Mining

**Initial implementation [23]–[26].** Compared country/category medians and trained a mean baseline, Ridge Regression and Random Forest to estimate **log first-observed Trending views** from metadata. Data were split chronologically by publication date.  
**Evidence:** Initial split **48,681 train / 12,228 test**; six checks passed. Initial Random Forest **MAE 0.7198, R² approximately 0.2086**. Scores are on the held-out test set and MAE is in log-view units.

### Expert Review Round 1 — Target observation-time leakage
**Finding:** Publication-date separation does not guarantee that training outcomes were observed before the test-period cutoff, since first-observed Trending views can occur after publication.  
**Revision 1 implemented — [27]:** Diagnosed how many training targets had Trending observation dates on or after the publication cutoff.  
**Evidence:** **88 of 48,681** training rows (**0.18%**) crossed the **2023-04-28** cutoff; latest training observation was **2023-05-02**, overlapping the test observation period.  
**Decision:** Exclude the 88 temporally ineligible training outcomes and rerun the comparison.

### Expert Review Round 2 — Corrected time-respecting evaluation
**Finding:** The identified overlap required a change to the training set, not just an explanatory caveat.  
**Revision 2 implemented — [28]:** Removed the 88 rows; retrained the three models with **48,593 train** while keeping **12,228 test** rows unchanged.  
**Evidence:** Four revised checks passed; final Random Forest **MAE 0.7200, RMSE 0.9427, R² 0.2079**, versus Ridge **0.7759 MAE** and mean baseline **0.8140 MAE**.  
**Decision:** Use the revised results rather than the initial performance table.

### Expert Review Round 3 — Extended content and thumbnail validation
**Finding:** Metadata-only model metrics were insufficient to address the project's content-oriented question. Additional post-revision exploratory checks were conducted; they were **not** separately counted as a third revision cycle.  
**Additional evidence [29]–[38]:** Selected **20 videos** (10 per country), checked publicly accessible embed metadata (**9 US, 8 KR**), attempted transcript retrieval, inspected description previews, improved crude keyword topic labeling with a more specific title-based classifier (**18/20 classified**), and expanded thumbnail extraction to **200 tested / 179 accessible** (**90 US, 89 KR**).  
**Thumbnail findings:** Overall Spearman correlations between simple brightness, contrast, RGB variation and log views were weak in this outcome-stratified sample. Within-group coefficients varied in direction and magnitude. Five final thumbnail checks passed in the saved run.  
**Transcript version note:** Early discussion documented **0/17** retrieved (16 `RequestBlocked`, one `VideoUnplayable`), but the latest saved notebook outputs report **14/17** successfully retrieved. **No systematic transcript-content labeling was performed** regardless of retrieval status.  
**Decision:** Do not claim to have evaluated actual storytelling, engagement, cultural causation, retention, or click-through rates. Sampling by view-count halves and image-access failures limit generalization.

---

## Phase 5 — Interpretation / Evaluation

**Initial implementation — [39].** Consolidated revised model performance, image-feature correlations, title-topic coverage and direct-content availability.  
**Evidence:** Random Forest improved MAE by **11.55%** versus the mean baseline; **179** valid thumbnails and **18/20** title-classified pilot videos.

### Expert Review Round 1 — Combined metrics obscure geographic differences
**Finding:** One aggregate performance score could conceal differences between US and KR examples.  
**Revision 1 implemented — [40]:** Evaluated all three already-fitted models separately on each country's test videos.  
**Evidence:** Random Forest on **US 7,908**: **MAE 0.7236, R² 0.1345**; on **KR 4,320**: **MAE 0.7133, R² 0.2258**. Country test counts summed correctly.  
**Decision:** Report country-specific performance without inferring that one national audience is inherently more predictable.

### Expert Review Round 2 — Feature reliance left unexplained
**Finding:** Model rankings did not reveal which input variables contributed most to the trained model's predictive accuracy.  
**Revision 2 implemented — [41]:** Computed permutation importance using **3,000 reproducibly sampled test videos**, three repeats and MAE scoring.  
**Evidence:** Largest positive increases in MAE when shuffled were **country 0.0943**, **category_name 0.0752**, **publish_hour_utc 0.0478**, and **tag_count 0.0452**.  
**Decision:** Explain these as model reliance, not causal effects or popularity percentages.

### Expert Review Round 3 — Synthesis, scope and reproducibility
**Finding:** Conclusions must respect the limited R², Trending-only sampling, metadata proxy limitations and the distinction between successful transcript access and actual transcript analysis.  
**Final check — [42]:** Consolidated five evidence categories and ran six consistency checks; **all six passed** in the saved outputs. This was **validation, not a third implemented revision**.  
**Decision:** Treat conclusions as exploratory associations among sampled Trending videos. Report that the tested metadata model has limited explanatory power. Do not assert causes of popularity, prepublication forecasting reliability, viewership retention, or video-level storytelling effects. Correct inconsistent transcript availability statements prior to submission.

---

## Revision and Review Count

| KDD phase | Review checkpoints documented | Implemented revision cycles | Relevant revision cells |
|---|---:|---:|---|
| Selection | 3 | 2 | [7], [8] |
| Preprocessing | 3 | 2 | [14], [15] |
| Transformation | 3 | 2 | [21], [22] |
| Data Mining | 3 | 2 | [27], [28] |
| Interpretation / Evaluation | 3 | 2 | [40], [41] |
| **Total** | **15** | **10** | |

Additional Data Mining exploratory work [29]–[38], Interpretation synthesis [39] and final validation [42] are documented separately rather than misrepresented as extra formal revision cycles.

## Final Limitations and Follow-up

1. **Trending-only selection:** No claim about the probability of a typical uploaded video entering Trending; no non-Trending comparison cohort.
2. **Target definition:** View counts come from the first observed Trending record, not consistent post-upload horizons or final lifetime totals.
3. **Metadata temporality:** Longest observed descriptions, tags and possibly edited titles may not match publication-time metadata.
4. **Visual proxies:** Brightness, contrast and RGB variation cannot capture thumbnail text, people, expression, meaning or aesthetics; thumbnail subgroups were chosen using the outcome and had missing images.
5. **Content analysis:** Classification is primarily title-based; retrieved transcript segments were not systematically evaluated for introductions, storytelling or references to contemporary events.
6. **Reproducibility:** Source CSVs and category JSONs must be provided in Google Drive; live thumbnail/transcript requests can change. A saved-output review does not replace a clean runtime execution.
7. **Pre-submission correction required:** Reconcile the latest **14/17 transcript retrieval output** with Markdown sections and the existing README still claiming **0/17**. Record the run date or distinguish initial and later attempts. Update the final narrative without claiming content analysis that was not carried out.

**Outcome:** The notebook documents meaningful iterative review and revision across all five KDD phases. Its numerical modeling and core validation results are coherent in the saved run, subject to the transcript-documentation discrepancy noted above.
