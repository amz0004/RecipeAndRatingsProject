# Are Recipes High in Saturated Fat Rated High?

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
These are the columns of the resulting cleaned dataframe.

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

### Univariate Analysis

<iframe
  src="assets/sat-fat-ratio-dist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
The saturated to total fat ratio is heavily skewed toward lower values, with the majority of recipes having ratios between 0.1-0.5, and a peak around 0.15. This suggests that most recipes in the dataset contain relatively moderate amounts of saturated fat compared to their total fat content, with very few recipes having extremely high saturated fat ratios (above 0.6), which could indicate healthier fat profiles overall.

### Bivariate Analysis

<iframe
  src="assets/total-fat_vs_mean-rating.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
There appears to be a positive relationship between total fat content and ratings, with higher-rated recipes (4-5 stars) containing more total fat on average. However, this apparent trend could be attributed to the heavily skewed distribution of ratings in the dataset, where there are significantly more high-rated recipes than low-rated ones, making it difficult to draw reliable conclusions about the true relationship.

To generate a more representative sample and eliminate rating bias, I implemented a balanced sampling approach that takes an equal number of recipes (1000) from each rating category (1-5 stars). This process iterates through each rating level, samples the specified number of recipes randomly from each group, and concatenates them into a single balanced dataset where each rating is equally represented.

<iframe
  src="assets/total-fat_vs_mean-rating-balanced.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The balanced sample reveals a more nuanced relationship where the association between total fat and ratings is less pronounced and more evenly distributed across rating levels. This suggests that the strong positive relationship observed in the original data was indeed largely due to the rating imbalance, and that total fat content alone may not be as strong a predictor of recipe ratings as initially appeared.

### Interesting Aggregates

For this section, I investigated the relationship between recipe complexity (number of steps) and the proportion of saturated fat in recipes. I created a pivot table by grouping recipes based on their number of preparation steps and calculating summary statistics (mean, median, minimum, and maximum) for the proportion of saturated fat within each group. The first 10 steps are displayed below.

|   n_steps |   mean |   median |   min |   max |
|----------:|-------:|---------:|------:|------:|
|         1 |   0.10 |     0.06 |   0.0 |  0.73 |
|         2 |   0.11 |     0.07 |   0.0 |  0.73 |
|         3 |   0.12 |     0.08 |   0.0 |  0.81 |
|         4 |   0.13 |     0.09 |   0.0 |  0.75 |
|         5 |   0.14 |     0.11 |   0.0 |  0.85 |
|         6 |   0.14 |     0.12 |   0.0 |  0.85 |
|         7 |   0.15 |     0.12 |   0.0 |  0.81 |
|         8 |   0.15 |     0.13 |   0.0 |  0.90 |
|         9 |   0.15 |     0.13 |   0.0 |  0.69 |
|        10 |   0.16 |     0.14 |   0.0 |  0.68 |

The pivot table reveals a clear positive relationship between recipe complexity and saturated fat content. As recipes progress from simple 1-step preparations to more complex 10-step processes, both the mean and median proportion of saturated fat consistently increase from 0.10/0.06 to 0.16/0.14 respectively. This pattern suggests that more elaborate cooking techniques tend to incorporate higher amounts of saturated fats, likely through the use of ingredients like butter, cream, or oil-intensive preparation methods that require multiple steps to properly integrate into the dish.

## Assessment of Missingness

Three columns in the merged dataset:`'date'`, `'rating'`, and `'review'` contain substantial amounts of missing data, so I will conduct a missingness analysis on the dataframe.

### NMAR Analysis

The missingness of ratings is likely related to the unobserved rating value itself. People who have neutral or negative experiences with recipes may be less motivated to leave a rating compared to those who had very positive or very negative experiences. For example, users who tried a recipe and found it "just okay" (3/5 stars) might not feel compelled to rate it. To potentially make the rating missingness MAR instead of NMAR, I would want to collect data on other engagement metric, such as time spent on recipe page, whether a user bookmarked it, if they printed/saved the recipe. We could potentially explain the rating missingness through observed covariates rather than the unobserved rating value itself this way.

### Missingness Dependency

I examined the missingness of `'rating'` column by testing the dependency of its missingness. I am investigating whether the missingness in the `'rating'` column depends on the column `'prop_sat_fat'`, which is the proportion of saturated fat out of the total calories, or the column `'minutes'`, which is the cooking time of the recipe.

#### Proportion of Saturated Fat and Rating

**Null Hypothesis**: The missingness of ratings is independent of the recipe’s proportion of saturated fat.

**Alternative Hypothesis**: The missingness of ratings depends on the recipe’s proportion of saturated fat.

**Test Statistic**: The absolute difference in the mean `'prop_sat_fat'` between recipes with and without missing ratings.

**Significance Level**: 0.05

To test these hypotheses, I conducted a permutation test by randomly shuffling the missingness labels in the 'rating' column 1,000 times. This generated 1,000 simulated distributions of absolute mean differences under the null hypothesis.

<iframe
  src="assets/permutation-rating-missingness-prop-sat-fat.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The observed statistic of **0.00497** is marked by the red vertical line on the graph. The p-value for this test is **0.0100**, which is below the significance level of 0.05. Therefore, we reject the null hypothesis and conclude that the missingness of the 'rating' column is likely dependent on the proportion of saturated fat (`'prop_sat_fat'`).

#### Minutes and Rating

**Null Hypothesis**: The missingness of ratings is independent of the recipe’s cooking time (minutes).

**Alternative Hypothesis**: The missingness of ratings depends on the recipe’s cooking time (minutes).

**Test Statistic**: The absolute difference in the mean cooking time ('minutes') between recipes with and without missing ratings.

**Significance Level**: 0.05

To test these hypotheses, I conducted a permutation test by randomly shuffling the missingness labels in the 'rating' column 1,000 times. This generated 1,000 simulated distributions of absolute mean differences under the null hypothesis.

<iframe
  src="assets/permutation-rating-missingness-minutes.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The observed statistic of **86.57040** is marked by the red vertical line on the graph. The p-value for this test is **0.0660**, which is above the significance level of 0.05. Therefore, we do not reject the null hypothesis and conclude that the missingness of the 'rating' column is likely independent from cooking time (`'minutes`).

## Hypothesis Testing



