# 🍽 Recipes & Ratings

This is a project for University of California San Diego DSC 80.  
**Name:** Nabila Afifah Qotrunnada  

---

## Dataset Source

This dataset contains recipes and ratings from food.com. It was originally scraped and used by the authors of a recommender‐systems paper.

### Getting the Data  
We downloaded two CSV files:  
- **RAW_recipes.csv** contains all the recipe metadata (name, prep time, ingredients, nutrition, tags, etc.).  
- **interactions.csv** contains reviews and numerical ratings submitted for those recipes.

We use a subset that includes only recipes and reviews posted since 2008 (the original full dataset was very large). Below is a brief description of each file’s columns:

#### RAW_recipes.csv (Recipes)  
- **name**: Recipe name  
- **id**: Recipe ID (unique identifier)  
- **minutes**: Minutes to prepare the recipe  
- **contributor_id**: User ID who submitted this recipe  
- **submitted**: Date the recipe was submitted  
- **tags**: A list of food.com tags (e.g., “desserts,” “main‐ingredient,” “preparation,” etc.)  
- **nutrition**: A list in the form `[calories, total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]`  
- **n_steps**: Number of steps in the recipe  
- **steps**: Text instructions, as an ordered list of steps  
- **description**: User‐provided description of the recipe  
- **ingredients**: A list of ingredient names  
- **n_ingredients**: Number of ingredients used  

#### interactions.csv (Ratings and Reviews)  
- **user_id**: User ID who submitted the review  
- **recipe_id**: Recipe ID (links to RAW_recipes.csv)  
- **date**: Date of the interaction  
- **rating**: Numeric rating given (0 indicates “no rating,” which we convert to NaN)  
- **review**: Free‐text review submitted alongside the rating  

---

## Introduction

In this project, we analyze a dataset of food.com recipes and user ratings to understand which recipe features correlate with higher user ratings. Our central question:  
**Do “quick” recipes (prep time ≤ 30 minutes) receive different average ratings than “long” recipes (prep time > 30 minutes)?**  

---

## Data Cleaning and Exploratory Data Analysis

We merged `RAW_recipes.csv` with `interactions.csv`, replaced ratings of 0 with NaN, and computed an average rating per recipe (`avg_rating`). We parsed the `nutrition` column into separate numeric columns (calories, fat, sugar, etc.) and converted the `tags` column into lists. We also generated summary statistics (e.g., distribution of prep times and calorie content).

---

## Assessment of Missingness

Because a recipe’s `avg_rating` is missing when no user rated it, this missingness is **Not Missing At Random (NMAR)**. We performed permutation tests to see whether missingness of `avg_rating` depends on features like prep time or calories.

---

## Hypothesis Testing

We tested whether quick (≤ 30 min) and long (> 30 min) recipes have different mean `avg_rating`:  
- **H₀:** μ_quick = μ_long  
- **H₁:** μ_quick ≠ μ_long  

---

## Framing a Prediction Problem

We aim to predict each recipe’s average rating (`avg_rating`) using only recipe‐level metadata.  
- **Problem type:** Regression  
- **Target:** `avg_rating`  
- **Features available at “time of prediction”:**  
  - `minutes` (prep time)  
  - Nutrition (`calories`, `total_fat`, `sugar`, `sodium`, `protein`, `saturated_fat`, `carbohydrates`)  
  - `n_ingredients` (# of ingredients)  
  - `n_steps` (# of steps)  
  - `is_dessert` flag derived from `tags`  

- **Metric:** Root Mean Squared Error (RMSE)

---

## Baseline Model

Our baseline uses only `minutes` and `calories` in a linear regression:  
- **Pipeline:** `StandardScaler()` → `LinearRegression()`  
- **Train/test split:** 80% / 20% (random_state=42)  
- **Test RMSE:** 0.6359  

An RMSE of 0.6359 means predictions are off by ~0.64 rating points on average.

---

## Final Model

To improve performance, we engineered three additional features and used a RandomForest regressor:  
1. **`n_ingredients`** (# of ingredients)  
2. **`protein_to_calorie`** (protein ÷ calories)  
3. **`is_dessert`** (1 if “desserts” appears in tags)  

We built a pipeline that standardizes numeric features and passes through `is_dessert`, then trained a `RandomForestRegressor`. Hyperparameters were tuned via 5‐fold CV over:  
- `n_estimators` ∈ {50, 100}  
- `max_depth` ∈ {5, 10, None}  
- `min_samples_split` ∈ {2, 5}  

**Best hyperparameters:** {'rf__max_depth': 5, 'rf__min_samples_split': 2, 'rf__n_estimators': 100}
**Best CV RMSE:** 0.6404  
**Test RMSE:** 0.6341 (an improvement over the baseline’s 0.6359)

---

## Fairness Analysis

We checked whether our final model’s errors differ by prep‐time group:  
- **Metric:** Absolute Error (AE) = |predicted – actual|  
- **Groups:** quick (≤ 30 min) vs. long (> 30 min)  
- **H₀:** mean AE_quick = mean AE_long  
- **H₁ (one‐sided):** mean AE_quick > mean AE_long  
