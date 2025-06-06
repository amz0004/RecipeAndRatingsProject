# Does Fat Equal Flavor? Analyzing the Impact of Saturated Fat Content on Recipe Ratings

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
The observed statistic of **0.00497** is marked by the red vertical line on the graph. The p-value for this test is **0.0100**, which is below the significance level of 0.05. Therefore, we have sufficient evidence to reject the null hypothesis.

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
The observed statistic of **86.57040** is marked by the red vertical line on the graph. The p-value for this test is **0.0660**, which is above the significance level of 0.05. Therefore, we do not sufficient evidence to reject the null hypothesis. 

## Hypothesis Testing

As mentioned in the introduction, I am investigating whether people rate high saturated fat recipes and low saturated fat recipes on the same scale. By high saturated fat recipes, I am referring to ecipes with a proportion of saturated fat higher than 0.10 (10% of total calories). This threshold aligns with the Dietary Guidelines for Americans, which recommends limiting saturated fat to 10% or less of daily calories. Proportion of saturated fat is referring to the values in `'prop_sat_fat'`, which are the proportion of saturated fat in calories out of the total calories of the recipe.
To investigate the question, I ran a permutation test with the following hypotheses, test statistic, and significance level.

**Null Hypothesis**: People rate all recipes on the same scale regardless of saturated fat content.

**Alternative Hypothesis**: People rate high saturated fat recipes higher than low saturated fat recipes.

**Test Statistic**: The difference in mean between rating of high saturated fat recipes and low saturated fat recipes.

**Significance Level**: 0.05

The reason U chose to run a permutation test is because I do not have any information about the population distribution, and I want to check if the two distributions look like they come from the same population. I proposed that people rate high saturated fat recipes higher because such recipes often contain rich, indulgent ingredients like butter, cream, and cheese that enhance flavor and palatability, potentially leading to higher ratings. For the test statistic, I chose the difference in mean of the ratings of two groups of recipes instead of absolute difference in mean. This is because I have a directional hypothesis, which is that people rate high saturated fat recipes higher than other recipes. By looking at the difference in mean between the two groups, I can see what type of recipes typically have a higher rating, which answers my question.

To run the test, I first split the data points into two groups: high saturated fat recipes (proportion of saturated fat > 0.10) and low saturated fat recipes (proportion of saturated fat ≤ 0.10). The observed statistic is **0.01360**.

Then I shuffled the ratings for 1000 times to collect 1000 simulating mean differences in the two distributions as described in the test statistic. I got a p-value of 0.00000.
Since the p-value is less than **0.05**, I reject the null hypothesis. There is statistically significant evidence suggesting that recipes higher in saturated fat tend to receive higher ratings than recipes lower in saturated fat.

<iframe
  src="assets/hypothesis_test_plot.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>
## Framing a Prediction Problem

I plan to predict the average rating of a recipe, which would be a regression problem since I am predicting a continuous numerical value (`'mean_rating'`) that can range from 1.0 to 5.0. The response variable is `'mean_rating'`, which represents the average of all user ratings for a given recipe.

I chose the average rating as the response variable because it provides a comprehensive measure of recipe quality and user satisfaction. To evaluate the model, I will use Mean Absolute Error (MAE) as the primary metric. MAE is appropriate because it provides an interpretable measure of how far off the predictions are on average (e.g., 'the model predicts ratings within 0.3 points on average'). 

**Time of Prediction Considerations**: At the time of prediction, I would know all recipe characteristics including nutritional information (`'saturated_fat'`), recipe complexity ('`n_steps'`), cooking time, ingredients, and other recipe metadata. However, I would not know any user-generated content such as individual ratings, reviews, or review dates, since these only exist after users have tried the recipe. This justifies my choice to use only recipe-intrinsic features like `'saturated_fat'` and `'n_steps rather'` than any rating-derived or user-generated features.

The model uses `'saturated_fat'` (nutritional content) and `'n_steps'` (recipe complexity) as features, both of which would be available at recipe publication time, making this a realistic prediction scenario for a recipe platform wanting to estimate how well a new recipe might be received before any user ratings are collected.

## Baseline Model

#### Model Description

The baseline model is a Random Forest Regressor designed to predict the average rating of recipes (`'mean_rating'`) using recipe characteristics available at the time of recipe publication.

#### Features

The model uses 2 features, both of which are quantitative:
1. `'saturated_fat'` (Quantitative): The amount of saturated fat in grams contained in the recipe.
2. `'n_steps'` (Quantitative): The number of preparation steps required to complete the recipe.

Feature Type Breakdown:

Quantitative features: 2 (`'saturated_fat'`, `'n_steps'`)
Ordinal features: 0
Nominal features: 0

#### Encodings and Preprocessing
Since both features are quantitative, no categorical encoding was necessary. The preprocessing pipeline includes:
   - SimpleImputer with mean strategy: Handles any missing values by replacing them with the mean of the respective feature
   - No scaling or transformation: Random Forest algorithms are tree-based and don't require feature scaling

#### Model Performance
The model achieved a Mean Absolute Error (MAE) of 0.4747 on the test set, meaning the predictions are off by approximately 0.47 rating points on average. An average error of ~0.47 points on a 1-5 rating scale demonstrates the model can provide useful predictions, getting within roughly half a rating point of the true value. Random Forest handles non-linear relationships well and provides robust predictions without overfitting. But while the current 2 features provide a good foundation, incorporating additional recipe characteristics like cooking time, comprehensive nutritional profile, and ingredient diversity could improve predictions. 

## Final Model

#### Feature Engineering and Selection

`'calories'`
From the data generating process perspective, calories reflect both the indulgence level and practical utility of a recipe. Higher-calorie recipes often contain rich, satisfying ingredients that enhance flavor and create memorable eating experiences, potentially leading to higher ratings. Conversely, health-conscious users may prefer lower-calorie options.

`'minutes'`
I included cooking time because it directly impacts recipe accessibility and user satisfaction. Recipes requiring excessive preparation time may receive lower ratings due to inconvenience and increased opportunity for cooking errors, especially among novice cooks. Conversely, overly simple recipes might be perceived as lacking sophistication.

`'prop_sat_fat'`
Instead of using raw saturated fat values, I chose the proportion relative to total calories because it better captures the nutritional richness independent of recipe size. This normalized measure reflects how "indulgent" a recipe is - higher proportions typically indicate the presence of butter, cream, cheese, and other ingredients that enhance flavor.

`'n_steps'`
The number of preparation steps captures recipe complexity and skill requirements. Recipes with too few steps might be perceived as overly simplistic, while those with excessive steps may intimidate users

#### Modeling Algorithm and Preprocessing

**Random Forest Regressor**: I selected Random Forest because it effectively captures non-linear relationships and interactions between features without requiring strong distributional assumptions. Given that recipe ratings likely depend on complex interactions (e.g., the relationship between cooking time and calories might differ for desserts versus main dishes), Random Forest's ensemble approach provides robust predictions.

**Preprocessing Strategy**:
   - QuantileTransformer (normal distribution) for `'calories'`, `'minutes'`, and `'prop_sat_fat'`: I observed these features had skewed distributions during EDA, and normalizing them helps the model capture patterns across their entire range more effectively
   - Log transformation (log1p) for `'n_steps'`: Step counts showed positive skew with some recipes having extremely high step counts; the log transformation reduces the disproportionate influence of overly complex recipes while preserving the relative complexity relationships

**Hyperparameter Selection**: I used 5-fold cross-validation with GridSearchCV to evaluate parameter combinations:
   - **n_estimators = 100**: Provided sufficient ensemble diversity without overfitting
   - **max_depth = 10**: Controlled tree depth to prevent overfitting while allowing complex pattern recognition
   - **min_samples_split = 2**: Enabled fine-grained splits to capture detailed relationships in the data

#### Final Model Performance

My final model achieved an MAE of 0.4663 compared to my baseline model's MAE of 0.4747, representing an improvement of 0.0084 points. The small magnitude of improvement highlights the inherent difficulty of predicting subjective ratings and suggests that additional features related to recipe categories, ingredient quality, or user preferences might be necessary for more substantial performance gains.

## Fairness Analysis

For my fairness analysis, I split the recipes into two groups based on saturated fat content: Low Saturated Fat and High Saturated Fat. I designated low saturated fat recipes as those with prop_sat_fat <= 0.1 and high saturated fat recipes as those with prop_sat_fat > 0.1. I chose to evaluate the Mean Absolute Error (MAE) parity of the model for the two groups because MAE directly measures prediction accuracy for regression tasks. Since my model predicts continuous rating values, MAE is the most appropriate fairness metric to assess whether the model performs equally well across different recipe types. Unequal performance could lead to biased recipe recommendations, where certain types of recipes (high vs. low saturated fat) are systematically over or under-estimated in their predicted ratings.

**Null Hypothesis**: My model is fair. Its MAE for low saturated fat recipes and high saturated fat recipes are roughly the same, and any differences are due to random chance.

**Alternative Hypothesis**: My model is unfair. Its MAE differs significantly between low and high saturated fat recipe groups.

**Test Statistic**: Difference in MAE (High saturated fat - Low saturated fat)

**Significance Level**: 0.05

#### Results:

**Low saturated fat group**: 8,259 recipes with MAE = 0.4689
**High saturated fat group**: 11,993 recipes with MAE = 0.4646
**Observed difference**: -0.0043
**P-value**: 0.73900

#### Conclusion

With a p-value of **0.739**, which is much greater than our significance level of 0.05, I found no significant evidence of unfairness in my model. The small observed difference of -0.0043 in MAE between groups appears to be due to random chance rather than systematic bias. This suggests that my model performs equally well on both low and high saturated fat recipes.
