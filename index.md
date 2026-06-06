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


'|    | name                                 |     id |   minutes |   contributor_id | submitted   | tags                                                                                                                                                                                                                        | nutrition                                    |   n_steps | steps                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | description                                                                                                                                                                                                                                                                                                                                                                       |          user_id |   recipe_id | date       |   rating |   avg_rating |\n|---:|:-------------------------------------|-------:|----------:|-----------------:|:------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------|----------:|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------:|------------:|:-----------|---------:|-------------:|\n|  0 | 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27  | [\'60-minutes-or-less\', \'time-to-make\', \'course\', \'main-ingredient\', \'preparation\', \'for-large-groups\', \'desserts\', \'lunch\', \'snacks\', \'cookies-and-brownies\', \'chocolate\', \'bar-cookies\', \'brownies\', \'number-of-servings\'] | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]     |        10 | [\'heat the oven to 350f and arrange the rack in the middle\', \'line an 8-by-8-inch glass baking dish with aluminum foil\', \'combine chocolate and butter in a medium saucepan and cook over medium-low heat , stirring frequently , until evenly melted\', \'remove from heat and let cool to room temperature\', \'combine eggs , sugar , cocoa powder , vanilla extract , espresso , and salt in a large bowl and briefly stir until just evenly incorporated\', \'add cooled chocolate and mix until uniform in color\', \'add flour and stir until just incorporated\', \'transfer batter to the prepared baking dish\', \'bake until a tester inserted in the center of the brownies comes out clean , about 25 to 30 minutes\', \'remove from the oven and cool completely before cutting\']                                                  | these are the most; chocolatey, moist, rich, dense, fudgy, delicious brownies that you\'ll ever make.....sereiously! there\'s no doubt that these will be your fav brownies ever for you can add things to them or make them plain.....either way they\'re pure heaven!                                                                                                              | 386585           |      333281 | 2008-11-19 |        4 |            4 |\n|  1 | 1 in canada chocolate chip cookies   | 453467 |        45 |          1848091 | 2011-04-11  | [\'60-minutes-or-less\', \'time-to-make\', \'cuisine\', \'preparation\', \'north-american\', \'for-large-groups\', \'canadian\', \'british-columbian\', \'number-of-servings\']                                                               | [595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0] |        12 | [\'pre-heat oven the 350 degrees f\', \'in a mixing bowl , sift together the flours and baking powder\', \'set aside\', \'in another mixing bowl , blend together the sugars , margarine , and salt until light and fluffy\', \'add the eggs , water , and vanilla to the margarine / sugar mixture and mix together until well combined\', \'add in the flour mixture to the wet ingredients and blend until combined\', \'scrape down the sides of the bowl and add the chocolate chips\', \'mix until combined\', \'scrape down the sides to the bowl again\', \'using an ice cream scoop , scoop evenly rounded balls of dough and place of cookie sheet about 1 - 2 inches apart to allow for spreading during baking\', \'bake for 10 - 15 minutes or until golden brown on the outside and soft & chewy in the center\', \'serve hot and enjoy !\'] | this is the recipe that we use at my school cafeteria for chocolate chip cookies. they must be the best chocolate chip cookies i have ever had! if you don\'t have margarine or don\'t like it, then just use butter (softened) instead.                                                                                                                                            | 424680           |      453467 | 2012-01-26 |        5 |            5 |\n|  2 | 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | [\'60-minutes-or-less\', \'time-to-make\', \'course\', \'main-ingredient\', \'preparation\', \'side-dishes\', \'vegetables\', \'easy\', \'beginner-cook\', \'broccoli\']                                                                        | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]    |         6 | [\'preheat oven to 350 degrees\', \'spray a 2 quart baking dish with cooking spray , set aside\', \'in a large bowl mix together broccoli , soup , one cup of cheese , garlic powder , pepper , salt , milk , 1 cup of french onions , and soy sauce\', \'pour into baking dish , sprinkle remaining cheese over top\', \'bake for 25 minutes or until cheese is lightly browned\', \'sprinkle with rest of french fried onions and bake until onions are browned and cheese is bubbly , about 10 more minutes\']                                                                                                                                                                                                                                                                                                                              | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this one  #412 broccoli casserole.i don\'t think there are any like this one in the database. i based this one on the famous "green bean casserole" from campbell\'s soup. but i think mine is better since i don\'t like cream of mushroom soup.submitted to "zaar" on may 28th,2008 |  29782           |      306168 | 2008-12-31 |        5 |            5 |\n|  3 | 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | [\'60-minutes-or-less\', \'time-to-make\', \'course\', \'main-ingredient\', \'preparation\', \'side-dishes\', \'vegetables\', \'easy\', \'beginner-cook\', \'broccoli\']                                                                        | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]    |         6 | [\'preheat oven to 350 degrees\', \'spray a 2 quart baking dish with cooking spray , set aside\', \'in a large bowl mix together broccoli , soup , one cup of cheese , garlic powder , pepper , salt , milk , 1 cup of french onions , and soy sauce\', \'pour into baking dish , sprinkle remaining cheese over top\', \'bake for 25 minutes or until cheese is lightly browned\', \'sprinkle with rest of french fried onions and bake until onions are browned and cheese is bubbly , about 10 more minutes\']                                                                                                                                                                                                                                                                                                                              | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this one  #412 broccoli casserole.i don\'t think there are any like this one in the database. i based this one on the famous "green bean casserole" from campbell\'s soup. but i think mine is better since i don\'t like cream of mushroom soup.submitted to "zaar" on may 28th,2008 |      1.19628e+06 |      306168 | 2009-04-13 |        5 |            5 |\n|  4 | 412 broccoli casserole               | 306168 |        40 |            50969 | 2008-05-30  | [\'60-minutes-or-less\', \'time-to-make\', \'course\', \'main-ingredient\', \'preparation\', \'side-dishes\', \'vegetables\', \'easy\', \'beginner-cook\', \'broccoli\']                                                                        | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]    |         6 | [\'preheat oven to 350 degrees\', \'spray a 2 quart baking dish with cooking spray , set aside\', \'in a large bowl mix together broccoli , soup , one cup of cheese , garlic powder , pepper , salt , milk , 1 cup of french onions , and soy sauce\', \'pour into baking dish , sprinkle remaining cheese over top\', \'bake for 25 minutes or until cheese is lightly browned\', \'sprinkle with rest of french fried onions and bake until onions are browned and cheese is bubbly , about 10 more minutes\']                                                                                                                                                                                                                                                                                                                              | since there are already 411 recipes for broccoli casserole posted to "zaar" ,i decided to call this one  #412 broccoli casserole.i don\'t think there are any like this one in the database. i based this one on the famous "green bean casserole" from campbell\'s soup. but i think mine is better since i don\'t like cream of mushroom soup.submitted to "zaar" on may 28th,2008 | 768828           |      306168 | 2013-08-02 |        5 |            5 |'





| name | id | minutes | contributor_id | sodium_PDV | protein_PDV | saturated_fat_PDV | carbohydrates_PDV |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 brownies in the world best ever | 333281 | 40 | 985201 | 3.0 | 3.0 | 19.0 | 6.0 |
| 1 in canada chocolate chip cookies | 453467 | 45 | 1848091 | 22.0 | 13.0 | 51.0 | 26.0 |
| 412 broccoli casserole | 306168 | 40 | 50969 | 32.0 | 22.0 | 36.0 | 3.0 |
| 412 broccoli casserole | 306168 | 40 | 50969 | 32.0 | 22.0 | 36.0 | 3.0 |
| 412 broccoli casserole | 306168 | 40 | 50969 | 32.0 | 22.0 | 36.0 | 3.0 |

**Univariate Analysis**
This analysis examines the distribution of recipe steps. The histogram shows a right-skewed distribution, with a cluster of around 5-9 steps in recipes, indicating most recipes are usually simpler with few complex recipes.

<iframe
    src="{{ '/assets/plots/nsteps_distribution.html' | relative_url }}"
    width="100%"
    height="450"
    style="border:none;">
</iframe>

**Bivariate Analysis**
This analysis examines the relationship between recipe steps and protein content. The binned histogram shows that average protein content are spread across the number of recipe steps, with a rise at around 50 recipe steps for the highest protein levels.

<iframe
    src="{{ '/assets/plots/avg_protein_step_distribution.html' | relative_url }}"
    width="100%"
    height="450"
    style="border:none;">
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

<iframe
    src="{{ '/assets/plots/fig_mar_mins_distribution.html' | relative_url }}"
    width="100%"
    height="450"
    style="border:none;">
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

The model I created is a baseline linear regression model that predicts recipe calorie content using two quantitative features (*n_steps*) and (*minutes*). Both features are **quantitative** continuous variables that do not require encoding transformations, and therefore, can be used as-is. No **ordinal** or **nominal** features were included in this baseline model. Linear regression is appropriate because the coefficients can adjust for scale differences without standardization.

**Result of the Baseline Model Performance**: It achieved an RMSE = 310.03 calories and an R-squared = 0.0435. The predicted average calorie amount across test recipes was 374 calories. The model explained only ~4% of the variance in calorie content, and as such, I do not believe this current model is "good" for practical use. The extremely low R-squared value indicates that using only thse two features is insufficient to predicting calorie content. 

The model serves as a reasonable baseline for comparison against a more feature-engineered model and insufficient for accurate calorie prediction alone.

## Final Model

In addition to the previous two features, I engineered two new features from the *total fat (PDV)* and *carbohydrates (PDV)* columns:

1. *fat_carb_ratio*
- Calculated from the ratio of fat calories to carbohydrate calories with +1 added to the denominator to avoid a division by zero. This feature acts as a balance for the macronutrients, especially when recipes with different ratios of fat-to-carbs can result in the same total calories. For example, a high-fat, low-carb recipe and a low-fat, high-carb recipe may have the same calorie count, but their macronutrient ratios differ, which may be useful for prediction.

2. *carb_quantile*
- By applying a quantile transformation to the *carbs_calories (#)* column, which reduces skew, and map the distribution to uniform. This transformation is useful for tree models, such as Random Forest because it reduces the effect of extreme outliers for the model to create patterns that aren't influenced by outliers.

**Modeling Algorithm Used**: I chose a ***RandomForestRegressor*** to analyze non-linear feature relationships to predict nutritional trends. 

Using ***GridSearchCV***, I tuned two hyperparameters:
- *max_depth* to prevent overfitting.
- *min_samples_split* for the min number of samples.

The optimal hyperparameters found were max_depth=25 and min_samples_split=5, which suggested deep trees with frequent splits were appropriate for this large dataset.

**Result of the Final Model Performance**: With a more complex pipeline and optimal hyperparameters, this final model explains nearly all variance in calorie content, achieving an RMSE = 41.08 calories and an R-squared = 0.9831, a large improvement over the baseline model's RMSE and R-squared. Due to better engineered features and a flexible modeling algorithm, the final model is much more effective at model at predicting calorie content in recipes.

## Fairness Analysis

For the fairness analysis, I looked at the following question: "Does my final model perform worse for recipes with higher step counts than it does for recipes with lower step counts?"

Splitting the groups into higher and lower step counts, I created **Group X**, higher step counts, consisting of recipes with more than 10 steps, while **Group Y**, lower step counts, consists of recipes with less than or equal to 10 steps.

**Null Hypothesis**: My model is fair and its RMSE for low-step and high-step recipes is roughly the same. Any differences are due to random chance.

**Alternative Hypothesis**: My model is unfair and its RMSE for recipes with higher step counts is higher than for smaller recipes, or less recipe steps.

**Test Statistic**: Absolute difference in RMSE for calorie content between recipes with high and low step amounts.

**Significance Level**: α = 0.05

I chose *RMSE (Root Mean Squared Error)* as the **evaluation metric** because it measures the magnitude of prediction errors in calories, which was the original units, and supports comparing model performance across groups.

**Result of the Fairness Analysis**: The permutation test resulted in a p-value that was above the signifance level at 0.36. My model failed to reject the null hypothesis, making my model fair against high- or low-step recipes and suggesting that there is no statistically significant evidence that the final model performs worse for recipes with higher step counts compared to lower step counts. Any observed differences in RMSE may be due to random change than due to unfairness.

---