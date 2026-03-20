# 🍳 DSC 80 Project 04: How Does Cooking Time Affect Recipe Ratings

## Overview
This data science project, conducted at UC San Diego, focuses on exploring the relationship between the **cooking time of a recipe** and its **rating**. Recipes vary widely in preparation time, and understanding whether users prefer longer or shorter recipes can provide insight into user behavior and recipe design.

---

## Introduction
Food is an essential part of everyday life, and cooking is a common task or hobby for many people. Cooking time is often a major factor when deciding what to prepare. Some individuals prefer quick and convenient meals, while others are willing to spend extra time creating more elaborate dishes—whether for enjoyment, variety, or special occasions.

Because of this, we investigate the following question:

> **Do recipes that take longer to prepare receive higher average ratings, or do users prefer recipes with shorter cooking times?**

To answer this, we analyze two datasets from [Food.com](https://www.food.com/), originally collected for recommender system research in the paper:  
*“Generating Personalized Recipes from Historical User Preferences”* by Majumder et al.

---

## Datasets

### 📘 RAW_recipes
- **Rows:** 83,782 (one per recipe)

| Column | Description |
|--------|------------|
| `name` | Recipe name |
| `id` | Recipe ID |
| `minutes` | Minutes required to prepare the recipe |
| `contributor_id` | ID of the user who submitted the recipe |
| `submitted` | Submission date |
| `tags` | Food.com tags |
| `nutrition` | `[calories, fat, sugar, sodium, protein, saturated fat, carbs]` (PDV format) |
| `n_steps` | Number of steps |
| `steps` | Recipe instructions |
| `description` | User-provided description |
| `ingredients` | List of ingredients |
| `n_ingredients` | Number of ingredients |

---

### 📗 interactions
- **Rows:** 731,927 (user–recipe interactions)

| Column | Description |
|--------|------------|
| `user_id` | User ID |
| `recipe_id` | Recipe ID |
| `date` | Interaction date |
| `rating` | User rating |
| `review` | Review text |

---

## Data Processing

To analyze the relationship between cooking time and ratings:

- Ratings of `0` are treated as **missing values** (invalid ratings)
- We compute a new variable:  
  **`avg_rating` = average rating per recipe**

### Steps:
1. Group `interactions` by `recipe_id`
2. Compute mean rating
3. Merge results into `RAW_recipes`

---

## Key Variables

- `minutes` → Cooking/preparation time  
- `rating` → Individual user rating  
- `avg_rating` → Average rating per recipe (derived)  
- `id` / `recipe_id` → Used to merge datasets  

---

## Goal of the Project

By studying the relationship between cooking time and ratings, we aim to:

- Understand how **time investment affects user satisfaction**
- Help users **choose recipes more efficiently**
- Provide insight for **recipe creators**
- Support **recommendation system design**
