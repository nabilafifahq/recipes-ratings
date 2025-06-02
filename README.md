# 🍽 Recipes & Ratings

This is a project for University of California San Diego DSC 80
We use dataset from 
**Name:** Nabila Afifah Qotrunnada

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

#### RAW_interactions.csv (Ratings and Reviews)  
- **user_id**: User ID who submitted the review  
- **recipe_id**: Recipe ID (links to RAW_recipes.csv)  
- **date**: Date of the interaction  
- **rating**: Numeric rating given (0 indicates “no rating,” which we convert to NaN)  
- **review**: Free‐text review submitted alongside the rating  
