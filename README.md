![Warm meal](https://image.lexica.art/full_webp/1afecf0d-5567-47bc-af3e-9665416f6de1)
# 🍽 How Prep Time Influences Recipe Ratings
**By:** Nabila Afifah Qotrunnada

## Introduction

We analyze a dataset of recipes and user ratings from Food.com to answer the question:  
**Do “quick” recipes (prep time ≤ 30 minutes) receive different average ratings than “long” recipes (prep time > 30 minutes)?**

Readers should care because understanding how preparation time affects user satisfaction can inform both home cooks and recipe developers—short recipes might be easier to prepare but could sacrifice perceived quality, while longer recipes might be rated differently despite requiring more effort.

- **Total recipes (after cleaning):** 83,194  
- **Relevant columns:**  
  - `minutes` (numeric): Time in minutes required to prepare the recipe.  
  - `avg_rating` (numeric): Average user rating on a 1–5 scale (NaN if no ratings).  
  - `calories` (numeric): Total calories per recipe.  
  - `n_ingredients` (integer): Number of ingredients used.  
  - `tags` (list of strings): Categorical descriptors (e.g., “desserts,” “quick meals”).  

---

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

1. **Merged** `RAW_recipes.csv` and `interactions.csv`, replacing `rating = 0` with `NaN` and computing `avg_rating` per recipe.  
2. **Parsed** `nutrition` (string‐encoded lists) into seven numeric columns:  
   - `calories`, `total_fat`, `sugar`, `sodium`, `protein`, `saturated_fat`, `carbohydrates`.  
3. **Converted** `tags` from a string‐encoded list into a true Python list.  
4. **Filtered out** recipes with `minutes > 1440` (no realistic cooking session exceeds 24 hours).  
5. **Verified** there were no missing values in `nutrition` and no recipes with empty `tags` (empty lists only occur if parsing failed).

#### Head of Cleaned DataFrame

```markdown
| name                                   | id     | minutes | contributor_id | submitted  | tags                                                     | nutrition                     | n_steps | … | n_ingredients | avg_rating | calories | total_fat | sugar | sodium | protein | saturated_fat | carbohydrates | prep_bin |
|----------------------------------------|--------|---------|----------------|------------|----------------------------------------------------------|-------------------------------|---------|---|--------------:|-----------:|---------:|----------:|------:|-------:|--------:|--------------:|--------------:|----------|
| 1 brownies in the world best ever      | 333281 | 40      | 985201         | 2008-10-27 | [60-minutes-or-less, time-to-make, course, main-ingredient, …] | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0] | 10      | … |             9 |        4.0 |    138.4 |      10.0 |   50.0 |    3.0 |     3.0 |           19.0 |           6.0 | 30–60    |
| 1 in canada chocolate chip cookies     | 453467 | 45      | 1848091        | 2011-04-11 | [60-minutes-or-less, time-to-make, cuisine, preparation, …]       | [595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0] | 12      | … |            11 |        5.0 |    595.1 |      46.0 |  211.0 |   22.0 |    13.0 |           51.0 |          26.0 | 30–60    |
| 412 broccoli casserole                 | 306168 | 40      | 50969          | 2008-05-30 | [60-minutes-or-less, time-to-make, course, main-ingredient, …]     | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]  | 6       | … |             9 |        5.0 |    194.8 |      20.0 |    6.0 |   32.0 |    22.0 |           36.0 |           3.0 | 30–60    |
| millionaire pound cake                 | 286009 | 120     | 461724         | 2008-02-12 | [time-to-make, course, cuisine, preparation, occasion, …]            | [878.3, 63.0, 326.0, 13.0, 20.0, 123.0, 39.0]  | 7       | … |             7 |        5.0 |    878.3 |      63.0 |  326.0 |   13.0 |    20.0 |          123.0 |          39.0 | 60–120   |
| 2000 meatloaf                          | 475785 | 90      | 2202916        | 2012-03-06 | [time-to-make, course, main-ingredient, preparation, …]               | [267.0, 30.0, 12.0, 12.0, 29.0, 48.0, 2.0]  | 17      | … |            13 |        5.0 |    267.0 |      30.0 |   12.0 |   12.0 |    29.0 |           48.0 |           2.0 | 60–120   |
````

---

### Univariate Analysis

#### 1. Distribution of Recipe Prep Time

<iframe
  src="assets/plots/step2_prep_time.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

> **This histogram shows how many recipes fall into each prep‐time bin. Most recipes take between 20 and 60 minutes to prepare, with a long right tail of very time‐consuming recipes.**

#### 2. Distribution of Recipe Average Ratings

<iframe
  src="assets/plots/step2_avg_rating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

> **The majority of recipes cluster between 4.0 and 5.0 stars, indicating generally positive feedback. Very few recipes have ratings below 3.0, suggesting that most users rate favorably.**

---

### Bivariate Analysis

#### 1. Prep Time vs. Average Rating (Scatter with LOWESS)

<iframe
  src="assets/plots/step2_scatter_minutes_vs_rating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

> **There is a slight negative relationship: as prep time increases, average rating tends to decrease marginally. However, the effect is small, indicating other factors also drive ratings.**

#### 2. Average Rating by Calorie Range (Box Plot)

<iframe
  src="assets/plots/step2_box_calories_vs_rating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

> **Higher‐calorie recipes (1000+ calories) have slightly higher median ratings, suggesting that richer, indulgent dishes receive more favorable feedback.**

---

### Interesting Aggregates

#### Top 10 Contributors by Mean Recipe Rating (min 5 recipes)

| contributor\_id | mean\_rating | n\_recipes |
| --------------: | -----------: | ---------: |
|            4466 |        5.000 |          5 |
|            4404 |        5.000 |          5 |
|            4321 |        5.000 |          5 |
|            4365 |        5.000 |          5 |
|           13822 |        5.000 |          5 |
|            4862 |        5.000 |          5 |
|            4882 |        5.000 |          5 |
|            8094 |        5.000 |          7 |
|            8387 |        5.000 |          6 |
|            4775 |        5.000 |          5 |

> **These top contributors each have at least five recipes, all rated a perfect 5.0, indicating consistent high‐quality submissions.**

#### Average Rating by Number of Ingredients (binned)

| n\_ing\_bin | mean\_avg\_rating |
| :---------: | ----------------: |
|     0–5     |             4.647 |
|     5–10    |             4.617 |
|    10–15    |             4.623 |
|    15–20    |             4.637 |
|     20+     |             4.701 |

> **Recipes with more than 20 ingredients have the highest mean rating (4.70), suggesting that more complex recipes may be better received.**

---

## Assessment of Missingness

Because a recipe’s `avg_rating` is missing only if no user rated it, this missingness is **Not Missing At Random (NMAR)**: the fact that a recipe is unrated depends on unobserved factors (e.g., page views). Additional data (e.g., view counts) could explain missingness and render it MAR.

**Permutation Test: Does missingness of `avg_rating` depend on prep time?**

* **H₀:** Mean prep time is the same for recipes with missing vs. non‐missing ratings.
* **H₁:** Mean prep time differs between missing vs. non‐missing groups.
* **Test statistic:** Difference in mean `minutes`.
* **α = 0.05.**

<iframe
  src="assets/plots/step3_null_minutes.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

> **This histogram shows the null distribution of permuted differences in mean prep time. The observed difference (\~–0.06 minutes) lies well within the distribution (p ≈ 0.56), indicating no evidence that missingness depends on prep time.**

---

## Hypothesis Testing

**Question:** Do “quick” recipes (≤ 30 min) and “long” recipes (> 30 min) have different mean `avg_rating`?

* **H₀:** μ\_quick = μ\_long
* **H₁:** μ\_quick ≠ μ\_long
* **Test statistic:** Difference in means of `avg_rating` (quick − long).
* **α = 0.05.**
* **Observed means:**

  * μ\_quick = 4.645
  * μ\_long  = 4.609
  * Difference = 0.035
* **Permutation p‐value:** ≈ 0.0000 (reject H₀).

<iframe
  src="assets/plots/step4_null_avg_rating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

> **The observed difference (0.035) lies far in the tails of the permuted distribution (p < 0.001), providing strong evidence that quick and long recipes differ in average ratings.**

---

## Framing a Prediction Problem

We aim to **predict** each recipe’s `avg_rating` using only metadata known at posting:

* **Problem type:** Regression
* **Response variable:** `avg_rating` (continuous, 1–5)
* **Metric:** Root Mean Squared Error (RMSE), because we care how far on average predictions deviate from true ratings.
* **Features used (available at prediction time):**

  * `minutes` (prep time; numeric)
  * `calories`, `protein` (nutrition; numeric)
  * `n_ingredients` (# of ingredients; numeric)
  * `n_steps` (# of steps; numeric)
  * `is_dessert` (binary derived from `tags`)

---

## Baseline Model

* **Model:** Linear Regression
* **Features:**

  * `minutes` (numeric, continuous)
  * `calories` (numeric, continuous)
* **Pipeline:** StandardScaler → LinearRegression
* **Train/test split:** 80% train, 20% test (random\_state=42)
* **Test RMSE:** 0.6359

> **An RMSE of 0.6359 means baseline predictions are off by about 0.64 rating points on average. Given ratings range from 1 to 5, this error is moderate.**

---

## Final Model

We engineered three additional features:

1. **`n_ingredients`** (numeric): More ingredients may indicate richer flavor.
2. **`protein_to_calorie`** (numeric): Protein‐to‐calorie ratio as a proxy for healthiness.
3. **`is_dessert`** (binary): Whether “desserts” appears in `tags`, since desserts may systematically get higher ratings.

* **Model:** RandomForestRegressor
* **Pipeline:**

  1. StandardScaler for numeric features: `minutes`, `calories`, `protein`, `n_ingredients`, `protein_to_calorie`
  2. `is_dessert` passed through as a binary feature
  3. RandomForestRegressor
* **Hyperparameters tuned (5-fold CV):**

  * `n_estimators` ∈ {50, 100}
  * `max_depth` ∈ {5, 10, None}
  * `min_samples_split` ∈ {2, 5}
* **Best parameters:**

  * `n_estimators`: 100
  * `max_depth`: 5
  * `min_samples_split`: 2
* **CV RMSE:** 0.6404
* **Test RMSE:** 0.6341

> **The final model’s RMSE of 0.6341 slightly improves over baseline (0.6359). Additional features related to ingredients, nutrition, and dessert status provided incremental gains.**

<details>
  <summary>Feature Importances (Final RandomForest)</summary>

| Feature              | Importance |
| :------------------- | ---------: |
| protein\_to\_calorie |       0.26 |
| calories             |       0.24 |
| minutes              |       0.18 |
| n\_ingredients       |       0.13 |
| protein              |       0.10 |
| is\_dessert          |       0.07 |
| n\_steps             |       0.02 |

</details>

---

## Fairness Analysis

We ask: **Does our final model perform differently on “quick” vs. “long” recipes?**

* **Group Y (quick):** recipes with `minutes ≤ 30`

* **Group X (long):** recipes with `minutes > 30`

* **Metric:** Root Mean Squared Error (RMSE) per group

* **H₀:** RMSE\_quick ≈ RMSE\_long (no difference)

* **H₁:** RMSE\_long > RMSE\_quick (model is worse on long recipes)

* **Observed RMSE (quick):** 0.6261

* **Observed RMSE (long):** 0.6550

* **Observed difference:** 0.0289

* **Permutation p‐value:** 0.1725 (> 0.05; fail to reject H₀)

<iframe
  src="assets/plots/step8_null_rmse_difference.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

> **This histogram shows the permuted null distribution of RMSE differences. The observed difference (0.0289) lies well within the null distribution (p ≈ 0.17), indicating no significant evidence of unfairness.**
