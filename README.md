<p align="center">
  <img src="assets/netflix_logo.png"/>
</p>


# Netflix Originals — What Makes a “Hit”?

This project analyzes Netflix Original TV series using publicly available data from **TMDb** to examine structural patterns associated with high-performing titles (“hits”).
The goal is **analytical understanding and validation**, not production-level prediction.

---

## Project Overview

* **Dataset size:** ~2,000 Netflix Original TV series
* **Data source:** TMDb API (`discover/tv`, Netflix network ID = 213)
* **Unit of analysis:** TV series
* **Focus:** How temporal context, genre composition, and country of origin relate to a rating-based hit definition

---

## Project Structure

```
├── 01_data_collection.ipynb        # TMDb API collection + cleaning
├── 02_define_hit_and_exploration.ipynb
├── 03_eda_hit_analysis.ipynb       # Exploratory analysis
├── 04_feature_engineering.ipynb    # Feature construction
├── 05_modeling_and_validation.ipynb
├── data/
│   ├── netflix_tv_all.csv
│   ├── netflix_tv_clean.csv
│   ├── netflix_tv_with_hit.csv
│   └── netflix_tv_features_v1.csv
└── README.md
```

Each notebook has a **single responsibility** to avoid leakage and maintain interpretability.

---

## Hit Definition

A title is labeled as a **hit** if it satisfies:

* Top percentile threshold based on **popularity**
* Sufficient **vote count** to ensure rating reliability

This definition intentionally reflects **audience engagement**, not critical acclaim or causality.

Resulting class distribution:

* Hits: ~9%
* Non-hits: ~91%

This imbalance is realistic for breakout content and handled explicitly in modeling.

---

## Exploratory Data Analysis (EDA)

Key findings from `03_eda_hit_analysis.ipynb`:

* **Temporal skew:** Hit titles are disproportionately concentrated in recent years, indicating exposure and platform-scale effects.
* **Genre asymmetry:** Certain genres (e.g., Drama, Action/Fantasy) exhibit higher hit rates than high-volume genres such as Documentary or Reality.
* **Geographic patterns:** US and select international markets show higher conditional hit probabilities.
* **Metadata completeness:** Titles with missing genre or temporal metadata are significantly less likely to be hits.

EDA is **descriptive**, not causal, and explicitly acknowledges bias sources.

---

## Feature Engineering

In `04_feature_engineering.ipynb`, insights from EDA are translated into structured features:

### Temporal

* `years_since_release`
* `covid_period` (pre-COVID / COVID / post-COVID)

### Categorical

* `primary_genre` (rare genres grouped as “Other”)
* `primary_country`
* Explicit “unknown” categories preserved

### Binary

* Country indicators derived from primary country

No features used to define the hit label (e.g., popularity) are included in modeling to avoid leakage.

---

## Modeling & Validation

A **regularized logistic regression** model is used to test whether engineered features contain separable signal.

* **Train/validation split:** Stratified
* **Class imbalance handling:** `class_weight="balanced"`
* **Missing values:** Median imputation within pipeline
* **Metric:** ROC-AUC (accuracy is not informative at 9% positive rate)

### Result

* **ROC-AUC ≈ 0.83**

Interpretation:

> The model demonstrates strong separability between hit and non-hit titles, driven primarily by temporal context, genre composition, and country of origin.

This result reflects **structural association**, not deployable prediction or causality.

---

## Limitations

* Hit label is derived from engagement metrics, not long-term success or revenue.
* Temporal effects (e.g., platform growth, COVID period) introduce unavoidable bias.
* External data sources (reviews, cross-platform ratings) are not included.
* Modeling is evaluative, not optimized for production use.

---

## Setup Notes

* TMDb API key is required for data collection.
* Create a `.env` file with:

  ```
  TMDB_API_KEY=your_api_key_here
  ```
* `.env` and system files (e.g., `.DS_Store`) are excluded via `.gitignore`.

---

## Summary

This project demonstrates a **disciplined analytical workflow**:

* Clear separation of exploration, construction, and validation
* Explicit handling of bias and leakage
* Focus on interpretability over performance inflation

The result is a realistic, reproducible analysis of what differentiates high-performing Netflix Originals at a structural level.

---