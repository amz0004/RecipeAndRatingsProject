# Are Recipes High Saturated Fat Rated Higher?

Author: Amy Zhao 

## Overview

This data science project, conducted at UCSD, focus on exploring the relationship between the rating of a recipe and the proportion of saturated fat contributing to the nutritional value of the given recipe.

## Introduction

Food plays a major role in everyday life, and taste is often a deciding factor in how much we enjoy a dish. Rich, high-fat foods are commonly associated with better flavor, which raises the question: do recipes with more saturated fat receive higher ratings? This is especially relevant in the U.S., where many people consume more saturated fat than recommended, despite its known health risks. In this project, I explore the relationship between recipe ratings and saturated fat content by analyzing two datasets from food.com, containing recipes and user reviews from 2008 to 2018. 

The first dataset, `recipes`, contains 83782 rows, indicating 83782 unique recipes, with 12 columns recording the following information:

| Column             | Description                                                                                                                                                                                       |
| :----------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `'name'`           | The recipe's name                                                                                                                                                                                 |
| `'id'`             | The recipe's ID                                                                                                                                                                                   |
| `'minutes'`        | The minutes to prepare the recipe                                                                                                                                                                 |
| `'contributor_id'` | The ID of the user who submitted the recipe                                                                                                                                                       |
| `'submitted'`      | The date the recipe was submitted                                                                                                                                                                 |
| `'tags'`           | Food.com's tags for recipe                                                                                                                                                                        |
| `'nutrition'`      | The recipe's nutritional information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]. PDV standing for “percentage of daily value” |
| `'n_steps'`        | The number of steps in the recipe                                                                                                                                                                 |
| `'steps'`          | The recipe's steps, ordered                                                                                                                                                                   |
| `'description'`    | The user-provided description                                                                                                                                                                     |
| `'ingredients'`    | The list of the recipe's ingredients                                                                                                                                                              |
| `'n_ingredients'`  | The number of ingredients in the recipe                                                                                                                                                           |

The second dataset, `interactions`, contains 731927 rows and each row contains a review from the user on a specific recipe. The columns it includes are:

| Column        | Description         |
| :------------ | :------------------ |
| `'user_id'`   | The reviewer's ID   |
| `'recipe_id'` | The recipe ID       |
| `'date'`      | The date of interaction |
| `'rating'`    | The rating given    |
| `'review'`    | The review's text   |

## Data Cleaning and Exploratory Data Analysis

Before conducting any data analyses, I performed the following data cleaning steps on the two datasets.

1. Left merge the recipes and interactions datasets on 'id' and 'recipe_id', respectively.

   - This step helps match each recipe with its interaction(s). Everything is saved in the `recipes_interactions_merged_df` DataFrame.

2. Check data types of all the columns.

   - This step is to help evaluate what data cleaning steps and/or data type conversions may be needed.
   - | Column             | Description |
     | :----------------- | :---------- |
     | `'name'`           | object      |
     | `'id'`             | int64       |
     | `'minutes'`        | int64       |
     | `'contributor_id'` | int64       |
     | `'submitted'`      | object      |
     | `'tags'`           | object      |
     | `'nutrition'`      | object      |
     | `'n_steps'`        | int64       |
     | `'steps'`          | object      |
     | `'description'`    | object      |
     | `'ingredients'`    | object      |
     | `'n_ingredients'`  | int64       |
     | `'user_id'`        | float64     |
     | `'recipe_id'`      | float64     |
     | `'date'`           | object      |
     | `'rating'`         | float64     |
     | `'review'`         | object      |

3. Fill all ratings of 0 with np.nan.

    - Since ratings range from 1 (lowest) to 5 (highest), any value of 0 represents a missing rating—either from a user who left only a review or from recipes with no user interaction. To prevent these placeholders from skewing the data, all 0s were replaced with np.nan.

4. Add column `'mean_rating'`, containing the mean rating for each respective recipe.

   - Since individual recipes can receive multiple ratings from different users, taking the average allows us to capture a more representative, overall impression of how well the recipe was received.

5. Split values in the nutrition column to individual columns of floats.

   - Split values in the `'nutrition'` column into individual float columns. Although the entries in the 'nutrition' column appear as lists, they are stored as strings. Using the provided column description to identify what each position in the list represents (e.g., calories, fat, saturated fat), we applied a lambda function to convert the strings into Python lists, then split those lists into separate columns and converted the values to floats. This transformation allows for easier numerical analysis of individual nutritional components.

6. Convert nutrition columns based on %DV (Percent Daily Value) to grams.

    - This standardizes nutrition data so that nutrient values expressed as percentages can be directly compared or analyzed alongside values given in grams.
      
8. Add column `'sat_fat_fatio'`, containing the ratio of saturated fat to total fat for each respective recipe.

    - Since individual recipes can receive multiple ratings from different users, taking the average allows us to capture a more representative, overall impression of how well the recipe was received.

10. Add colum `'prop_sat_fat'`, containing the proportion of staturated fat contributing the calories for each respective recipe.

     - This provides a normalized measure of how significant saturated fat is in the overall caloric profile of the recipe.

#### Result
These are the columns of the resulting cleaned df.

| Column              | Description        |
| :------------------ | :----------------- |
| `'name'`            | object             |
| `'id'`              | int64              |
| `'minutes'`         | int64              |
| `'contributor_id'`  | int64              |
| `'submitted'`       | object             |
| `'tags'`            | object             |
| `'nutrition'`       | object             |
| `'n_steps'`         | int64              |
| `'steps'`           | object             |
| `'description'`     | object             |
| `'ingredients'`     | object             |
| `'n_ingredients'`   | int64              |
| `'user_id'`         | float64            |
| `'recipe_id'`       | float64            |
| `'date'`            | object             |
| `'rating'`          | float64            |
| `'review'`          | object             |
| `'mean_rating'`     | float64            |
| `'calories'`        | float64            |
| `'total_fat'`       | float64            |
| `'sugar'`           | float64            |
| `'sodium'`          | float64            |
| `'protein'`         | float64            |
| `'saturated_fat'`   | float64            |
| `'carbohydrates'`   | float64            |
| `'sat_fat_ratio'`   | float64            |
| `'prop_sat_fat'`    | float64            |

The cleaned dataframe ended up with 234426 rows and 27 columns, with 83629 unique recipes. Here are the first 5 rows of unique recipes of the cleaned and transformed dataset. Since there are many columns in the merged dataframe, Those that are most relevant and/or were altered are displayed below. Scroll right to view more columns.

| name                                   |    id  | mean_rating | calories | total_fat | saturated_fat | sat_fat_ratio | prop_sat_fat |
|:--------------------------------------|-------:|------------:|---------:|----------:|---------------:|---------------:|--------------:|
| 1 brownies in the world best ever     | 333281 |         4.0 |    138.4 |      7.80 |           3.8  |          0.49  |         0.25  |
| 1 in canada chocolate chip cookies    | 453467 |         5.0 |    595.1 |     35.88 |          10.2  |          0.28  |         0.15  |
| 412 broccoli casserole                | 306168 |         5.0 |    194.8 |     15.60 |           7.2  |          0.46  |         0.33  |
| millionaire pound cake                | 286009 |         5.0 |    878.3 |     49.14 |          24.6  |          0.50  |         0.25  |
| 2000 meatloaf                         | 475785 |         5.0 |    267.0 |     23.40 |           9.6  |          0.41  |         0.32  |


