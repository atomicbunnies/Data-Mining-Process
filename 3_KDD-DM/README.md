# What Makes YouTube Videos Take Off?

**A KDD analysis of YouTube Trending videos in the United States and South Korea (2021–2023)**

This project explores how video metadata, publishing characteristics, content topics, and simple thumbnail image features relate to **view counts when a video is first observed in YouTube Trending**. It also tests how well metadata can estimate those observed view counts. The project follows the five stages of **Knowledge Discovery in Databases (KDD)**: Selection, Preprocessing, Transformation, Data Mining, and Interpretation/Evaluation.

> **Scope:** This is an analysis of videos *already present in Trending*, not all YouTube uploads. The target is the first **observed** Trending-day view count—not lifetime views, audience retention, click-through rate, or the exact time a video first became popular. Results are associations, not causal effects.

## Research questions

- How do title formatting, publication timing, and content category relate to first-observed Trending views?
- Do these relationships differ between US and KR Trending records?
- How accurately can straightforward metadata-based models estimate observed view counts?
- Are basic thumbnail image statistics associated with views?
- What can metadata reveal about broad content topics, and what requires watching the actual videos?

## Data

**Source:** [YouTube Trending Video Dataset on Kaggle](https://www.kaggle.com/datasets/rsrishav/youtube-trending-video-dataset).

The analysis uses the US and KR country datasets, with corresponding category-ID JSON files. The initial imported data contains **534,541 daily records** (US 268,787; KR 265,754). Records were deduplicated by country, video ID, and Trending calendar day; the highest observed view count was retained when multiple records existed for the same key. The analysis then selected **60,925** country–video observations first seen in Trending during 2021–2023. After quality checks, **60,909** observations remained (US 38,853; KR 22,056).

Each country–video pair is treated as its own observation: a video appearing in both country lists can be included twice. The dataset has no comparison group of videos that never trended.

### Notebook

`KDD_YouTube_Content_Analysis.ipynb` — complete five-stage analysis and its successive review/revision experiments.

## Method

| KDD stage | What the notebook does |
| --- | --- |
| **1. Selection** | Inspects raw country datasets, maps category IDs, checks duplicate daily records, selects first-observed Trending entries for 2021–2023, and uses calendar-day differences for timing. |
| **2. Preprocessing** | Examines missing and invalid fields, view-count skew, title/tags/description availability, and thumbnail access. Removes 16 invalid records and uses `log1p` for the target. |
| **3. Transformation** | Builds title-length, word-count, punctuation, number, hashtag, tag-count, category, country, and UTC publication-time variables. Tests image feature extraction. |
| **4. Data Mining** | Compares category-level views; fits a mean baseline, Ridge Regression, and Random Forest; checks time separation; explores metadata-based topics and thumbnail visual statistics. |
| **5. Interpretation / Evaluation** | Reports model metrics, performance by country, permutation importance, thumbnail correlations, content-access limitations, and final validation checks. |

### Modeling setup

The target is `log1p(first_observed_views)`. The **11 input features** include title formatting, tag count, country, content category, and UTC publication hour/weekday. The model does **not** use likes, comments, view counts, or days until Trending as input variables.

The dataset was split by **publication date**, producing a held-out set of **12,228** later-published records. A review found that 88 initially selected training videos had target counts observed on or after the cutoff; these were excluded from the final training set. Final training uses **48,593** records and a test set of **12,228**. The cutoff is **April 28, 2023**, and the revised training targets precede test target observations.

The notebook evaluates:

- **Mean Baseline:** predicts the training-set mean log view count.
- **Ridge Regression:** linear regression with regularization.
- **Random Forest Regression:** 80 trees, maximum depth 12, minimum leaf size 5, random state 42.

The experiment is a chronological **held-out test**, not a cross-validated hyperparameter search. The metadata may reflect information retained or updated in the historical Trending dataset, so the analysis is **not** a strictly validated pre-publication prediction system.

## Results

### Final held-out model performance

| Model | MAE (log views) ↓ | RMSE (log views) ↓ | R² ↑ |
| --- | ---: | ---: | ---: |
| **Random Forest** | **0.7200** | **0.9427** | **0.2079** |
| Ridge Regression | 0.7759 | 1.0069 | 0.0963 |
| Mean Baseline | 0.8140 | 1.0594 | -0.0004 |

Random Forest reduced MAE by **11.55% relative to the mean baseline**, but its R² of approximately **0.21** shows that much of the variation in log observed views remains unexplained. Error metrics are on the **log-transformed** target, not raw view counts.

**Random Forest by country:**

| Test subset | Videos | MAE (log views) | R² |
| --- | ---: | ---: | ---: |
| US | 7,908 | 0.7236 | 0.1345 |
| KR | 4,320 | 0.7133 | 0.2258 |

Country-level performance differences may reflect differing category mixes, distributions, and market context; they do not show that one population is inherently more predictable.

On a reproducible 3,000-row test subset, **permutation importance** ranked `country` (0.0943) and `category_name` (0.0752) highest, followed by `publish_hour_utc` (0.0478) and `tag_count` (0.0452). These values represent **increases in log-MAE when shuffled**, not causal effect sizes or percentages of popularity.

### Thumbnail and topic pilots

- **Thumbnails:** 200 sampled videos (100 per country; 50 from each half of the observed-view distribution), **179** accessible high-resolution thumbnails (US 90, KR 89). Image-based brightness, grayscale contrast, and a simple RGB-variation measure showed **weak overall Spearman correlations** with log views (all absolute values below 0.11). Within-group estimates varied, based on small and deliberately stratified groups.
- **Topic labels:** A 20-video pilot (10 per country) used title-based keyword rules to identify broad topics. **18 of 20** received at least one label. The initial keyword approach produced false positives and was revised to use more specific patterns and English word boundaries. These are *metadata labels*, not verified annotations of the videos themselves.
- **Actual video content:** Public embed metadata was available for 17 of the 20 selected videos, but **no transcripts could be retrieved** in the notebook environment (16 `RequestBlocked`, one `VideoUnplayable`). Thus, spoken introductions and storytelling could **not** be analyzed directly. This does not prove that the videos lack captions.

## Main takeaways and limitations

The evaluated metadata offers some predictive signal for observed Trending views, but it does not identify a formula for going viral. Simple image color/brightness statistics do not capture facial expressions, text overlays, editing, curiosity, click-through rate, or viewing satisfaction. The dataset also lacks subscriber history, watch time, retention, repeat-viewer metrics, and a comparable group of non-Trending uploads. Cultural trends, memes, and actual storytelling were **not directly validated** against video content. Descriptions may have been edited after a video's first Trending appearance.

Thumbnail availability and caption-access restrictions can change over time; sampled image statistics and accessibility counts may not reproduce exactly. The original notebook retains initial analyses and later corrections so the review process is traceable.

## How to run

1. Download the US and KR archives and their corresponding category JSON files from the Kaggle dataset above.
2. In Google Drive, create `MyDrive/KDD_Data/` containing exactly:

   ```text
   KDD_Data/
   ├── US_youtube_trending_data.csv.zip
   ├── KR_youtube_trending_data.csv.zip
   ├── US_category_id.json
   └── KR_category_id.json
   ```

3. Open `KDD_YouTube_Content_Analysis.ipynb` in **Google Colab**. Allow Google Drive mounting and run cells **from top to bottom**.
4. Dependencies used include `pandas`, `numpy`, `matplotlib`, `seaborn`, `scikit-learn`, `requests`, `Pillow`, and `youtube-transcript-api` (installed in the notebook when needed). The Korean-label plot may install a Nanum font in Colab.
5. Expect downloaded thumbnails, public embed metadata, and transcript requests to depend on network access and YouTube's current response. A blocked transcript request is a documented outcome, not evidence of missing captions.

**Reproducibility note:** The notebook stores execution outputs, including the reported metrics. A clean end-to-end Colab run against the original Drive data and external YouTube endpoints has **not** been independently verified as part of this README review. The original notebook's output is therefore the source of the figures above. Fixed random seeds make sampling/model behavior more consistent but do not fix external website availability.

## Project Files

- `KDD_YouTube_Content_Analysis.ipynb` — Complete KDD analysis notebook with code and saved outputs.
- `README.md` — Project overview, methodology, results, and reproducibility instructions.
- `revision_log.md` — AI-assisted review history, including three review rounds and two revisions for each of the five KDD stages.

## Project Links

- **Medium Article:** https://medium.com/@byeonggwan.cho/analyzing-youtube-trending-videos-with-kdd-a-data-mining-project-f01df821ce3f
- **YouTube Presentation:** https://youtu.be/CYepeqnoNuU

## Data attribution

The project uses a publicly available Kaggle dataset, with author attribution and license terms available on the [dataset page](https://www.kaggle.com/datasets/rsrishav/youtube-trending-video-dataset). YouTube thumbnails and metadata remain subject to their respective usage rights and availability.
