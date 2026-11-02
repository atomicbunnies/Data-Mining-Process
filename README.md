# Data Mining Process Projects

**CMPE 255 — Data Mining | Fall 2026**

This repository contains three data mining projects completed using different methodologies: **CRISP-DM, SEMMA, and KDD**.

Each project includes a Jupyter notebook, a detailed README, an AI-assisted revision log, a Medium article, and a YouTube presentation.

## Project Overview

| Project | Methodology | Dataset | Main Task |
|---|---|---|---|
| [1. Predictive Maintenance](./1_CRISP-DM/) | CRISP-DM | AI4I 2020 Predictive Maintenance | Predict machine failures |
| [2. Overwatch League Match Prediction](./2_SEMMA-DM/) | SEMMA | Overwatch League historical matches (2018–2020) | Predict match winners |
| [3. YouTube Trending Analysis](./3_KDD-DM/) | KDD | US and South Korea Trending videos (2021–2023) | Analyze factors associated with observed views |

## 1. Predictive Maintenance — CRISP-DM

Applied the six phases of **Cross-Industry Standard Process for Data Mining (CRISP-DM)** to a synthetic predictive maintenance dataset.

Trained and evaluated machine learning models to identify potential machine failures from operating measurements. The selected Random Forest model achieved an **F1-score of 0.6584** and **recall of 0.7794** for the failure class on the held-out test set.

**Resources:**
- [Project Folder](./1_CRISP-DM/)
- [Medium Article](https://medium.com/@byeonggwan.cho/predicting-machine-failures-with-crisp-dm-a-data-mining-project-486c8d79afd0)
- [YouTube Presentation](https://youtu.be/W6OytMXlLys)

## 2. Overwatch League Match Prediction — SEMMA

Applied **Sample, Explore, Modify, Model, and Assess (SEMMA)** to historical Overwatch League match data.

Compared Logistic Regression and Random Forest using chronological validation. The review process identified and corrected overlapping match dates across cross-validation folds.

The selected Logistic Regression model achieved **65.36% test accuracy** and a **Macro F1-score of 0.6487**, outperforming a majority-class baseline on both measures.

**Resources:**
- [Project Folder](./2_SEMMA-DM/)
- [Medium Article](https://medium.com/@byeonggwan.cho/predicting-overwatch-match-winners-with-semma-a-data-mining-project-54fc2fc43a24)
- [YouTube Presentation](https://youtu.be/V-HA9ozn6KA)

## 3. YouTube Trending Analysis — KDD

Applied **Knowledge Discovery in Databases (KDD)** to YouTube Trending records from the United States and South Korea.

Explored video metadata, publishing characteristics, basic thumbnail properties, and title-based topic labels. A Random Forest regression model achieved a **log-scale MAE of 0.7200** and **R² of 0.2079**, improving MAE by **11.55%** compared with the mean baseline.

The analysis focuses on views when videos were first observed in Trending. It does not establish what causes videos to become popular.

**Resources:**
- [Project Folder](./3_KDD-DM/)
- [Medium Article](https://medium.com/@byeonggwan.cho/analyzing-youtube-trending-videos-with-kdd-a-data-mining-project-f01df821ce3f)
- [YouTube Presentation](https://youtu.be/CYepeqnoNuU)

## AI-Assisted Review and Revision

ChatGPT was used as an AI-assisted reviewer throughout the projects to identify weaknesses, suggest improvements, and support iterative evaluation.

For each methodology phase, the workflow documented **three review rounds and two revision cycles**. Implemented changes were checked against the notebook's code and saved outputs.

The `revision_log.md` in each project folder records the review findings, revisions, and supporting evidence.

## Repository Structure

```text
Data-Mining-Process/
├── 1_CRISP-DM/
│   ├── CRISP_DM_Predictive_Maintenance.ipynb
│   ├── README.md
│   └── revision_log.md
├── 2_SEMMA-DM/
│   ├── SEMMA_Overwatch_Esports_Analysis.ipynb
│   ├── README.md
│   └── revision_log.md
├── 3_KDD-DM/
│   ├── KDD_YouTube_Content_Analysis.ipynb
│   ├── README.md
│   └── revision_log.md
├── .gitignore
└── README.md
```

## Reproducibility

The notebooks were developed in **Google Colab** using Python and common data science libraries.

Each project folder contains setup instructions and dataset source links. Some datasets require manual download from Kaggle, and the KDD project uses Google Drive for data access.

Saved notebook outputs document the reported results. Re-running network-dependent operations, such as YouTube thumbnail and transcript retrieval, may produce different availability outcomes.

## Notes

These projects were completed for educational purposes. Model evaluation results are specific to the selected datasets and experimental setups and should not be interpreted as production-ready forecasting performance.
