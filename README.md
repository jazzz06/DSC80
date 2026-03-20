# 🍳 DSC 80 Project 04: Investigating Recipe Ratings from Cooking Time and Recipe Features

## Overview

This data science project, conducted at UC San Diego, focuses on **predicting the average rating of a recipe** using recipe-level characteristics from the Food.com dataset. In particular, this project studies whether **cooking time** helps predict recipe ratings and whether that relationship remains meaningful after accounting for other factors such as recipe complexity, nutritional content, and recipe category.

---

## Introduction

Food is an important part of everyday life, and cooking is both a practical task and an enjoyable activity for many people. However, recipes vary widely in the time, effort, and ingredients they require. Some users may prefer recipes that are fast and convenient, while others may be more willing to spend extra time on recipes they expect to be more satisfying. Because of this, recipe ratings may depend not only on cooking time, but also on a broader set of recipe features.

In this project, **I investigate whether a recipe’s average rating can be predicted using information available in the recipe metadata**. My main variable of interest is `minutes`, the total preparation or cooking time of the recipe, but I also consider additional predictors such as the number of ingredients, number of steps, nutritional content, and selected recipe tags. This allows me to study whether cooking time remains useful for prediction after controlling for recipe complexity and other relevant characteristics.

---

## Datasets Description

This project uses two datasets from [Food.com](https://www.food.com/):

- `RAW_recipes`: contains recipe-level information  
- `interactions`: contains user ratings and reviews  

These datasets were originally collected for recommender system research in the paper *Generating Personalized Recipes from Historical User Preferences* by Majumder et al.

### 📘 RAW_recipes

This dataset contains **83,782 rows**, where each row corresponds to a unique recipe.

| Column | Description |
|--------|------------|
| `name` | Recipe name |
| `id` | Recipe ID |
| `minutes` | Minutes required to prepare the recipe |
| `contributor_id` | User ID of the recipe contributor |
| `submitted` | Date the recipe was submitted |
| `tags` | Food.com tags describing the recipe |
| `nutrition` | Nutrition info: [calories, fat, sugar, sodium, protein, saturated fat, carbohydrates] |
| `n_steps` | Number of preparation steps |
| `steps` | Recipe instructions |
| `description` | Recipe description |
| `ingredients` | List of ingredients |
| `n_ingredients` | Number of ingredients |

---

### 📗 interactions

This dataset contains **731,927 rows**, where each row represents a user interaction with a recipe.

| Column | Description |
|--------|------------|
| `user_id` | User ID |
| `recipe_id` | Recipe ID |
| `date` | Date of interaction |
| `rating` | User rating |
| `review` | User review text |

---

Given the datasets, investigating whether **average recipe ratings can be predicted using cooking time and other recipe characteristics**. To facilitate this analysis, I constructed a clean, recipe-level dataset by combining information from both the recipes and interactions data.
First, I processed the `interactions` dataset to compute a new variable, `avg_rating`, which represents the average rating for each recipe. Ratings of `0` were treated as missing values, since they do not represent valid user ratings. I then grouped by `recipe_id`, calculated the mean rating, and merged this information into the recipes dataset using the `id` column. Next, I extracted and engineered several features to support my analysis. From the recipes dataset, I used `minutes` as our main variable of interest, representing the total cooking or preparation time. I also included measures of recipe complexity such as `n_steps` and `n_ingredients`. In addition, I expanded the `nutrition` column into separate variables including `calories`, `total_fat`, `sugar`, `sodium`, `protein`, `saturated_fat`, and `carbohydrates`, allowing us to control for nutritional differences across recipes. From the `tags` column, I created indicator variables such as `is_dessert`, `is_quick`, `is_healthy`, and `is_vegetarian` to capture recipe context. Finally, I extracted `submitted_year` from the `submitted` column to account for temporal trends. The most relevant variables for our prediction task are `minutes`, `avg_rating`, `n_steps`, `n_ingredients`, the nutrition variables, and the engineered tag indicators. These features allow us to evaluate whether cooking time remains a meaningful predictor of ratings after accounting for recipe complexity, nutritional content, and contextual factors. By structuring the data in this way, I create a realistic modeling setup that allows us to compare simple and more complex predictive models, and to assess the true contribution of cooking time to recipe ratings.
And seeking answer to my research question intend to: 
- Help users choose recipes more effectively  
- Help recipe creators better understand audience preferences  
- Improve recommendation systems on recipe platforms  
provide insight into how time investment, complexity, and recipe features relate to user satisfaction.
 :contentReference[oaicite:0]{index=0}

## Variables Used

### Response Variable
- `avg_rating`: average rating per recipe  

### Main Predictor
- `minutes`: cooking/preparation time  

### Control Variables
- `n_steps`: number of steps  
- `n_ingredients`: number of ingredients  
- Nutrition variables (calories, fat, sugar, sodium, protein, etc.)  
- Tag-based indicators (e.g., dessert, quick, healthy)  

## Data Cleaning and Exploratory Data Analysis

In this section, I cleaned the merged recipe dataset and explored the key variables used in the project. Since the goal is to predict `avg_rating` using cooking time and other recipe-level characteristics, the EDA focuses on the response variable `avg_rating`, the main predictor `minutes`, and additional control variables related to recipe complexity.

### Data Cleaning

I first replaced ratings of `0` with missing values because they do not represent valid user ratings. I then grouped the `interactions` dataset by `recipe_id` to compute `avg_rating`, the average rating for each recipe, and merged this result into the `RAW_recipes` dataset.

Next, I parsed the `nutrition` column into separate numeric variables, converted `submitted` into a datetime variable, and engineered several useful features such as `submitted_year`, `log_minutes`, and indicator variables for selected tags like dessert and quick recipes. I also treated nonpositive cooking times as missing because those values are not valid for this analysis.

To support grouped comparisons, I binned cooking times into categories: `0-15`, `16-30`, `31-60`, `61-120`, and `120+` minutes.

### Univariate Analysis

The first variable I examined was `minutes`, since cooking time is the main feature of interest in this project.

<iframe
  src="assets/minutes_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The raw distribution of cooking time contains extreme outliers, which distort the graph and compress most observations into a small portion of the x-axis. To make the distribution interpretable, I restricted this visualization to recipes with cooking times of 300 minutes or less. Even after filtering for visualization, the distribution remains strongly right-skewed, showing that most recipes take a relatively short amount of time to prepare, while fewer recipes take much longer. This suggests that cooking time may be better represented on a transformed scale, which motivates the use of `log_minutes` later in the analysis.

I also examined the distribution of the response variable, `avg_rating`.

<iframe
  src="assets/avg_rating_distribution.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The distribution of `avg_rating` is concentrated near the high end of the rating scale, especially between 4 and 5. This indicates that most recipes in the dataset receive fairly positive ratings overall. Because the response variable has limited spread, it may be difficult for any model to strongly distinguish between recipes that are already rated highly. This is an important consideration for the later prediction task.

### Bivariate Analysis

To study the relationship between cooking time and rating, I first plotted `log_minutes` against `avg_rating`.

<iframe
  src="assets/log_minutes_vs_avg_rating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This plot shows only a weak association between cooking time and average rating. Although the fitted trend line suggests there may be some overall pattern, the points are widely dispersed, indicating that cooking time alone is not a strong predictor of recipe rating. This supports the idea that additional recipe-level features will likely be needed to improve prediction performance.

I also compared rating distributions across cooking-time groups using a box plot.

<iframe
  src="assets/cook_time_bin_boxplot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

While there are slight differences in the rating distributions across cooking-time categories, the overlap between groups is substantial. This again suggests that cooking time by itself does not explain much of the variation in ratings. Recipes with different preparation lengths appear to be rated on fairly similar scales, so a richer model with more predictors will likely be more appropriate.

### Interesting Aggregates

To summarize broader patterns, I grouped recipes by cooking-time bin and computed aggregate statistics such as average rating, average number of ingredients, and average number of steps.

The grouped table showed that recipes with longer cooking times also tend to have more ingredients and more steps, suggesting that cooking time may partly reflect recipe complexity. This matters because any apparent relationship between cooking time and rating could be confounded by how complicated the recipe is.

I also visualized the mean average rating in each cooking-time group.

<iframe
  src="assets/mean_rating_by_cook_time_bin.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This bar chart makes it easier to compare mean ratings across cooking-time groups. The differences between groups are fairly small, which reinforces the conclusion from the earlier plots: cooking time alone is not likely to strongly predict recipe ratings. Instead, it will be important to include other relevant recipe-level variables, such as number of ingredients, number of steps, and nutritional characteristics, in the final model.

### EDA Summary

Overall, the EDA suggests that cooking time has only a modest relationship with recipe rating. Cooking time is highly right-skewed, while average ratings are concentrated near higher values. Although grouped comparisons show some variation, the differences are relatively small. These results motivate building a prediction model that uses cooking time together with additional features rather than relying on cooking time alone.

