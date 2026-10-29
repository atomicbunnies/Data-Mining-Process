
# Predicting Overwatch League Match Winners Using SEMMA

**CMPE 255 - Data Mining**

## Project Objective

This project applies the SEMMA data mining methodology to historical Overwatch League match data.

The goal is to examine team performance and predict match winners using information available before each match.

The five SEMMA phases are:
1. Sample
2. Explore
3. Modify
4. Model
5. Assess

**Dataset:** Overwatch League Head-to-Head Statistics with Odds (Kaggle)

**Source:** https://www.kaggle.com/datasets/mdabbert/overwatch-league-with-odds

**Important:** This project uses historical match data. It does not predict outcomes in the current Overwatch competitive environment.



## 1. Sample

### Objective

The Sample phase identifies the dataset and determines which records are suitable for analysis.

This project uses historical Overwatch League match records. Each row represents one match between two teams.

We will inspect the dataset size, column names, match dates, participating teams, and target distribution before deciding how to prepare the data.

Because the dataset contains only 715 matches, we will initially retain all available records rather than take a smaller random sample.



import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

from google.colab import files

uploaded = files.upload()

df = pd.read_csv("owl-with-odds.csv")

print("Dataset loaded successfully")
print("Dataset shape:", df.shape)
display(df.head())



print("Number of rows:", len(df))
print("Number of columns:", len(df.columns))

print("\nColumn names:")
print(df.columns.tolist())

print("\nData types:")
print(df.dtypes.value_counts())

print("\nMissing values (top 10):")
display(df.isna().sum().sort_values(ascending=False).head(10))



df["match_date"] = pd.to_datetime(
    df["date"],
    errors="coerce"
)

all_teams = sorted(
    set(df["team_one"].dropna()) |
    set(df["team_two"].dropna())
)

print("Earliest match:", df["match_date"].min())
print("Latest match:", df["match_date"].max())
print("Unique teams:", len(all_teams))
print("Invalid or missing dates:", df["match_date"].isna().sum())

print("\nMatches by year:")
display(df["match_date"].dt.year.value_counts().sort_index())

print("\nRows with invalid dates:")
display(
    df.loc[
        df["match_date"].isna(),
        ["id", "date", "team_one", "team_two", "winner"]
    ]
)



print("Winner label distribution:")
display(df["winner_label"].value_counts().sort_index())

print("\nWinner label percentage:")
display(
    df["winner_label"]
    .value_counts(normalize=True)
    .sort_index()
    .mul(100)
    .round(2)
)

plt.figure(figsize=(7, 4))

sns.countplot(
    data=df,
    x="winner_label",
    order=[0, 1]
)

plt.xticks([0, 1], ["Team 1 Wins", "Team 2 Wins"])
plt.title("Match Winner Distribution")
plt.xlabel("Match Result")
plt.ylabel("Number of Matches")
plt.tight_layout()
plt.show()



print("Duplicate match IDs:", df["id"].duplicated().sum())

valid_labels = df["winner_label"].isin([0, 1]).all()

winner_matches = (
    ((df["winner_label"] == 0) & (df["winner"] == df["team_one"])) |
    ((df["winner_label"] == 1) & (df["winner"] == df["team_two"]))
)

print("Valid binary labels:", valid_labels)
print("Winner labels match winner names:", winner_matches.all())

print("\nSample phase validation:")
assert len(df) > 0
assert df["id"].is_unique
assert valid_labels
assert winner_matches.all()
assert df["match_date"].notna().any()

print("PASS")



### Sample Phase Findings

The dataset contains 715 historical Overwatch League matches and 58 original columns, covering January 2018 through June 2020.

All available matches are retained during the Sample phase because the dataset is relatively small. This avoids discarding potentially useful historical records before the data quality checks.

The dataset includes 20 teams.

The target variable is `winner_label`:
- 0: Team 1 wins (339 matches)
- 1: Team 2 wins (376 matches)

The classes are relatively balanced, so a majority-class baseline will be useful for later evaluation.

Some historical performance features contain missing values, and one match has an invalid or missing date.

The Modify phase will determine how to handle missing values and the undated record before chronological train/test splitting.

Only information available before each match will be considered for model inputs. The winner columns, match outcomes, and other post-match information will be excluded from predictors.



# Sample phase: detailed data quality summary

quality_summary = pd.DataFrame({
    "missing_count": df.isna().sum(),
    "missing_percent": df.isna().mean() * 100
})

quality_summary = (
    quality_summary[quality_summary["missing_count"] > 0]
    .sort_values("missing_count", ascending=False)
)

print("Columns with missing values:", len(quality_summary))
display(quality_summary.head(15).round(2))

print("\nUnique Team 1 names:", df["team_one"].nunique())
print("Unique Team 2 names:", df["team_two"].nunique())

print("\nMatches with the same team on both sides:")
same_team = df["team_one"] == df["team_two"]
print(same_team.sum())

print("\nDate parsing problems:")
display(df.loc[df["match_date"].isna(), ["id", "date", "team_one", "team_two"]])
