---
layout: homepage
---

## Introduction

***Overview to the Dataset***: This project uses a merged dataset with data taken from the food recipe platform Food.com. Using information about recipes and their nutritional content, the number of steps involved in preparation, and user interactions, such as ratings, I will be exploring the central question of "What is the relationship between macronutrients and the number of steps and complexity in a recipe?" 

This project's dataset provides an important look into recipe information and user engagement to help investigate questions ranging from recipe design to cooking complexity. My question will research how analyzing and predicting meals high in certain macronutrients can complicate recipes, making it important to help users quickly identify whether to use recipes with high-nutrient meals means longer cooking processes.

The number of rows in the merged dataset is 234429. The relevant columns used in this research and for the analyzing the central questions are the following:
-  ***nutrition*** : Nutritional information in a recipe. In particular, I will be looking at *protein (PDV)*, *calories (#)*, *total fat (PDV)*, *carbohydrates (PDV)*
- ***n_steps*** : Number of steps in a recipe.
- ***minutes*** : Minutes to prepare a recipe.
- `TODO`

## Data Cleaning and Exploratory Data Analysis

1. Handle unrated recipes (rating = 0), since original recipe ratings were on a scale of 0-5.
- All ratings of 0 were replaced with *np.nan* to treat them as missing values. This is because a low rating of 0 in this case does not represent a bad rated recipe but rather an unrated recipe. 
2. The average rating per recipe was calculated using *groupby('recipe_id')['rating'].mean()* and merged back into the dataset as a new column *avg_rating*.
- This allowed recipes with more reviews to have more reliable average ratings.
3. Convert string representations of lists into actual Python lists for columns containing list-like data.
- For each column with object dtype where all values start with "[", the eval() function was applied to convert the string to a list. After conversion, list elements were stripped of extra quotation marks for cleaner data.
4. Parse the nutrition column into separate nutritional components.
- I created a list for the target nutritional column names: 'calories (#)', 'total fat (PDV)', 'sugar (PDV)', 'sodium (PDV)', 'protein (PDV)', 'saturated fat (PDV)', 'carbohydrates (PDV)', and pd.Series was applied to the nutrition column to expand it into separate columns.

**For the following data cleaning steps**, implausible values were found (i.e a single serving cannot reasonably contain over 150g of protein), so given the extremely small proportion of affected data being measurement errors rather than missing information, listwise deletion preserves data integrity from outliers without introducing the bias imputation would create.
5. Remove implausible protein (PDV) values exceeding 300% PDV.
- The proportion of recipes with protein (PDV) > 300 was calculated to be extremely small (436 rows, or only 0.19% of the dataset).
6. Remove implausible calorie values exceeding 3000 calories.
- The proportion of recipes with calories (#) > 3000 was calculated to be extremely small (only 0.59% of the dataset). Considering adults require approximately 2,000 calories per day, 22,000 calories would be an unreasonable amount for a recipe, so a threshold of 3000 calories was used.
7. Remove implausible total fat (PDV) values exceeding 192.3 PDV.
- The proportion of recipes with total fat (PDV) > 192.3 was calculated to be extremely small (only 0.65% of the dataset). Considering adults require approximately 78g of total fat per day (1 PDV = 0.78g), 150g of fat = 150 / 0.78 = 192.3 PDV.
8. Remove implausible carbohydrate (PDV) values exceeding 181.8 PDV.
- The proportion of recipes with carbohydrates (PDV) > 181.8 was calculated to be extremely small (only 0.01% of the dataset). Considering adults require approximately 275g of carbohydrates per day (1 PDV = 2.75g), 500g of carbs = 500 / 2.75 = 181.8 PDV.

The following table is the first 5 rows of the cleaned dataframe, with most of the columns omitted due to space constraints.
| name | id | minutes | contributor_id | sodium_PDV | protein_PDV | saturated_fat_PDV | carbohydrates_PDV |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 brownies in the world best ever | 333281 | 40 | 985201 | 3.0 | 3.0 | 19.0 | 6.0 |
| 1 in canada chocolate chip cookies | 453467 | 45 | 1848091 | 22.0 | 13.0 | 51.0 | 26.0 |
| 412 broccoli casserole | 306168 | 40 | 50969 | 32.0 | 22.0 | 36.0 | 3.0 |
| 412 broccoli casserole | 306168 | 40 | 50969 | 32.0 | 22.0 | 36.0 | 3.0 |
| 412 broccoli casserole | 306168 | 40 | 50969 | 32.0 | 22.0 | 36.0 | 3.0 |

**Univariate Analysis**
This analysis examines the distribution of recipe steps. The histogram shows a right-skewed distribution, with a cluster of around 5-9 steps in recipes, indicating most recipes are usually simpler with few complex recipes.

<iframe src="{{ '/assets/plots/nsteps_distribution.html' | relative_url }}"
        width="100%"
        height="600"
        frameborder="0">
</iframe>

**Bivariate Analysis**
This analysis examines the relationship between recipe steps and protein content. The binned histogram shows that average protein content are spread across the number of recipe steps, with a rise at around 50 recipe steps for the highest protein levels.

<iframe src="/assets/plots/avg_protein_step_distribution.html" width="100%" height="600px" frameborder="0"></iframe>

## Assessment of Missingness

Lorem

## Hypothesis Testing

Lorem

## Framing a Prediction Problem

Lorem

## Baseline Model

Lorem

## Final Model

Lorem

## Fairness Analysis

Lorem

---