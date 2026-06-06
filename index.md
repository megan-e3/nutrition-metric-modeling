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

<iframe src="{{ '/assets/plots/avg_protein_step_distribution.html' | relative_url }}"
        width="100%"
        height="600"
        frameborder="0">
</iframe>

**Interesting Aggregates**
Recipe steps were grouped into bins (1-5, 6-10, 11-15, and 16+) to aggregate and compare the mean, median, standard deviation, and count of protein content, along with the average number of steps within each bin.

| steps_bin   |   ('protein (PDV)', 'mean') |   ('protein (PDV)', 'median') |   ('protein (PDV)', 'std') |   ('protein (PDV)', 'count') |   ('n_steps', 'mean') |
|:------------|----------------------------:|------------------------------:|---------------------------:|-----------------------------:|----------------------:|
| 1-5         |                     20.4762 |                             9 |                    27.9319 |                        54011 |               3.68762 |
| 6-10        |                     31.4523 |                            18 |                    33.7461 |                        91961 |               7.92724 |
| 11-15       |                     36.1451 |                            23 |                    36.3903 |                        50412 |              12.6659  |
| 16+         |                     41.1711 |                            28 |                    39.2327 |                        34687 |              21.4112  |

## Assessment of Missingness

**NMAR Analysis**
After analyzing missingness across the entire dataset, only the rating column contained a non-trivial proportion of missing values (~6.3%). I believe *rating* is "**NMAR**" because the probability of a missing rating likely depends on the value itself. Users who had a negative or neutral experience to the recipe may be less motivated to leave a rating compared to people who loved the recipe. To make this "MAR," instead of "NMAR," additional data like whether the user saved the recipe and left a review without a numerical rating would be needed to help explain the missingness.

**Missingness Dependency** 
Missingness Dependency was analyzed and tested for rating's potential dependency on `n_steps` and `minutes`.

First, rating and n_steps.
- Null Hypothesis: The missingness of the rating column does not depend on the recipe's number of steps.
- Alternate Hypothesis: The missingness of the rating column does depend on the recipe's number of steps.
- Test Statistic: Absolute difference in mean steps between missing and non-missing rating groups
- Significance Level: α = 0.05

The permutation test for missingness dependency on n_steps resulted in rejecting the null hypothesis (p < 0.05), indicating that recipes with missing ratings have a significantly different number of steps compared to those with observed ratings.

Next, rating and minutes.
- Null Hypothesis: The missingness of the rating column does not depend on the recipe's minutes to prepare recipe.
- Alternate Hypothesis: The missingness of the rating column does depend on the recipe's minutes to prepare recipe.
- Test Statistic / Significance Level: Same as the previous.

<iframe src="{{ '/assets/plots/fig_mar_mins_distribution.html' | relative_url }}"
        width="100%"
        height="600"
        frameborder="0">
</iframe>

However, the test for dependency on minutes failed to reject the null hypothesis (p > 0.05). Preparation time likely does not differ between missing and observed rating groups, since rating missingness is associated with recipe complexity, such as number of steps, but not the time needed to make the meal. This supports handling missing ratings as potentially NMAR with respect to recipe structure.

## Hypothesis Testing

**The main question in this section is as follows**: *What is the relationship between protein content and number of steps in a recipe?*

I implemented a permutation test with the following hypotheses, test statistic, significance level:
- ***Null Hypothesis***: There is no significant relationship between the protein content and number of steps in a recipe.
- ***Alternative Hypothesis***: There is a significant relationship between the protein content and number of steps in a recipe.
- ***Test Statistic***: Absolute difference in mean protein content between recipes with high and low step counts.
- ***Significance Level***: α = 0.05

To justify the choices above:
- The absolute difference in means was chosen as the test statistic because it is a direct measure of how protein content differs between simpler and complex recipes.
- A permutation test was chosen because it is robust for large datasets with potential outliers.
- The significance level of α = 0.05 is a standard threshold for Type I and Type II errors.

Resulting ***p-value***: The permutation test resulted in a p-value < 0.05, with a p-value extremely close to 0, because nearly none of the permuted differences reached the observed difference of 10.45 PDV. To **conclude**, based on the permutation test, we can reject the null hypothesis that there may be a statistically significant relationship between protein content and the number of steps in a recipe. 

Recipes with a higher number of steps tend to have ~ 10.6 PDV more protein on average compared to recipes with fewer steps, but a permutation test alone cannot fully prove that an association exists in the observed data.

## Framing a Prediction Problem

I built a **regression** model to predict the total calorie count *calories (#)* as a measure of the nutritional value of a recipe, because calories are expressed as continuous numerical values, not discrete categories. 

I chose *calories (#)* as the **response variable** because calories can help users with meal planning and differ from subjective measures like *ratings*, making it an objective, quantifiable feature of the recipe. Predicting calorie content may also provide further insights into my earlier analysis of *protein (PDV)*, another macronutrient.

A user would need to know certain information about recipe before cooking or accessing the full nutritional information, so I included the following features which would only be known at the ***time of prediction***.

**Features Used**:
- *n_steps* : number of preparation steps
- *minutes* : total preparation time

These features are known by the recipe author and are independent of calorie calculation. I hypothesize that recipes with longer cook times and more steps may indicate greater recipe complexity and longer cooking durations for meats, which tend to have higher calorie counts.

To evaluate my model, I will use the metric **RMSE (Root Mean Squared Error)**, because it penalizes large errors in the difference between the actual and predicted number of calories. Since R-squared does not measure the magnitude of prediction errors, **RMSE** is an appropriate metric for measuring large mistakes in predicting calorie content.

## Baseline Model

Lorem

## Final Model

Lorem

## Fairness Analysis

Lorem

---