---
layout: homepage
---

## Introduction

Research project at UCSD for DSC80 exploring predictive relationships between macronutrients in recipes. Analysis performed with linear correlation modeling, EDA, and regression pipelines.

What is the relationship between protein content and number of steps in a recipe?

## Data Cleaning and Exploratory Data Analysis

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Aenean bibendum placerat lorem, vitae convallis sem porta et. Aenean dapibus pharetra commodo. Etiam a massa sit amet nulla cursus interdum. Phasellus magna justo, pretium eu ultrices sed, eleifend id diam. 

## Assessment of Missingness

Significant amount of missing values were found in description, rating, and avg_rating_recipe. I believe rating is likely NMAR because people who are not interested in the recipe or hate it are less likely to leave a rating, making its missingness dependent on the rating value itself. It is possible that the missing values are low ratings, and thus felt insignificant to the user who may have wanted to forget a disappointing experience or meal, or potentially medium ratings where the user did not like nor dislike the recipe, and thus did not feel a need to recommend or deter others from using it.

Description/rating
- Null Hypothesis: The missingness of the description column does not depend on the recipe's rating.
- Alternate Hypothesis: The missingness of the description column does depend on the recipe's rating.
- Test Statistic: Absolute difference in mean rating between recipes with missing descriptions and with non-missing descriptions.
- Significance Level: α = 0.05

Description/Recipe steps
- Null Hypothesis: The missingness of the description column does not depend on the number of steps in a recipe.
- Alternate Hypothesis: The missingness of the description column does depend on the number of steps in a recipe.
- Test Statistic: Absolute difference in mean number of recipe steps between recipes with missing descriptions and with non-missing descriptions.
- Significance Level: α = 0.05
<!-- 
| Quarter     |   Count |
|:------------|--------:|
| Fall 2020   |       3 |
| Winter 2021 |       2 |
| Spring 2021 |       6 |
| Summer 2021 |       4 |
| Fall 2021   |      55 | -->

## Hypothesis Testing

Protein/Recipe Steps
- Null Hypothesis: There is no significant relationship between the protein content and number of steps in a recipe.
- Alternative Hypothesis: There is a significant relationship between the protein content and number of steps in a recipe.
- Test Statistic: Difference in mean protein content between recipes with high and low step counts.
- Significance Level: α = 0.05

## Framing a Prediction Problem

Predict the number of steps in recipes, using "n_steps" as the response variable. Regression problem because there possible numerical values for the quantity of steps in a recipe, not classification. I chose the number of steps in a recipe as the response variable for a measure of recipe complexity and effort needed to create the meal. Different from seeing how much time users can save from a particular recipe from looking at cook or bake time alone, I believe predicting n_steps would provide vital information of recipe complexity that minutes alone cannot measure. For example, a recipe requiring 60 minutes might involve 5 minutes of active prep followed by 55 minutes of passive oven baking while another recipe requiring the same amount of time could require constant attention across 30+ steps. It answers "how involved is this recipe?" and not just the time needed. The number of recipe steps may also give additional insights to analyze my earlier question between protein content and the number of recipe steps. This variable also differs from subjective measures like ratings, where "n_steps" is an objective, quantifiable feature of the recipe.

Given my current information on the cook time for a recipe (in minutes), and the recipe's tags, I hypothesize that recipes with longer cook times and specific tags among catergories like recipe difficulty tend to require more steps. 

I will use "minutes" and the amount of "tags" in a given recipe as features to predict "n_steps" as both would be known before a user can see the full step list, typically in recipe previews and search results, while the number of tags are shown in the recipe metadata with the title and time estimate. To evaluate my model, I will use the metric RMSE (Root Mean Squared Error), because it punishes large errors in the difference of the actual number and predicted number of recipe steps, unlike MAE and R² which may describe the error or be skewed by overfitting.
To evaluate my model, I will use RMSE (Root Mean Squared Error), because it penalizes large prediction errors between the actual and predicted number of recipe steps, which is very important for this problem where underestimating a 50-step recipe as 5 steps is far worse than being off by 2 steps. MAE treats all errors proportionally, and R² does not measure the maginitude of prediction error, so I believe RMSE is a good choice for measuring my errors in predictions to actual step counts.

## Baseline Model

calories and fat/carbohydrates

## Final Model

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Aenean bibendum placerat lorem, vitae convallis sem porta et. Aenean dapibus pharetra commodo. Etiam a massa sit amet nulla cursus interdum. Phasellus magna justo, pretium eu ultrices sed, eleifend id diam. 

## Fairness Analysis

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Aenean bibendum placerat lorem, vitae convallis sem porta et. Aenean dapibus pharetra commodo. Etiam a massa sit amet nulla cursus interdum. Phasellus magna justo, pretium eu ultrices sed, eleifend id diam. 

---