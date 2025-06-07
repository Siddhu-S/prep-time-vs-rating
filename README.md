# Do Quick Recipes Really Rate Better? A Data-Driven Look at Recipe Ratings
**Author:** Siddhartha Sarvepally

## Introduction
While in theory it sounds nice to spend hours creating the perfect dish to eat, most of the time that isn't possible. Instead, we are stuck trying to create something edible in the short periods of we have available to us. This is why it is so important to have a variety of quick recipes available to us, to give us a lot of different options to choose from.

In this data science project, the **relationship between preparation time and the average rating** of a recipe is explored, using two datasets from [food.com](food.com), one collecting recipe information, and the other containing rating information all from 2008 - 2018.
<br>

**Dataset #1: Recipes** (83,782 rows)

| Column         | Description                                                                 |
|----------------|-----------------------------------------------------------------------------|
| name           | Recipe name                                                                 |
| id             | Recipe ID                                                                   |
| minutes        | Minutes to prepare recipe                                                   |
| tags           | Food.com tags for recipe                                                    |
| nutrition      | Nutrition info: [calories, total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)] |
| n_steps        | Number of steps in recipe                                                   |
| n_ingredients  | Number of ingredients in recipe                                             |

**Dataset #2: Interactions** (731,927 rows)

| Column    | Description       |
|-----------|-------------------|
| recipe_id	| Recipe ID         |
| rating    | Rating given      |
| review    | Review text       |


With the findings from this project, we can have a better insight into what factors make a recipe more popular (in this case having a higher rating), and it can better inform a food.com user on what recipes, their audience would respond better too.

## Data Cleaning and Exploratory Data Analysis
### Data Cleaning
Before starting exploration into the data, we cleaned the data using the following steps in order to combine it all into one dataset and ensure we dealt with any null values or data type errors.

1. Left merge the recipes and interactions dataset on 'id' and 'recipe_id' in order to ensure all recipes stayed in the merged dataset irrespective of whether they have any reviews in the interactions dataset.

2. Replace all the 0s in the ratings column with NaN to ensure they do not skew the analysis, and then use a groupby function to find the average rating of each recipe.

3. Convert data in the 'submitted' column to datetime to make for easier parsing during exploratory data analysis

4. Filter the data, removing all recipes that took 0 minutes or less and recipes that were over 360 minutes or 6 hrs. There were many outliers in the dataset, with some recipes taking multiple weeks, and when looking into relationship between preparation time and average rating, it makes the most sense to look at a generalized dataset of up to six hours which is the general max threshold for the time someone would take to create one meal.

5. Create column 'is_short' to be used in later analysis, gives a boolean value depending on if the prep time for the recipe was less than or equal to 30 minutes
225905, 19
After fully cleaning the data, the merged dataframe with 19 columns and 225905 rows looks as follows:

|     id | name                                 |   minutes |   n_steps |   n_ingredients |   rating |   avg_rating | is_short   |
|-------:|:-------------------------------------|----------:|----------:|----------------:|---------:|-------------:|:-----------|
| 333281 | 1 brownies in the world    best ever |        40 |        10 |               9 |        4 |            4 | False      |
| 453467 | 1 in canada chocolate chip cookies   |        45 |        12 |              11 |        5 |            5 | False      |
| 306168 | 412 broccoli casserole               |        40 |         6 |               9 |        5 |            5 | False      |
| 306168 | 412 broccoli casserole               |        40 |         6 |               9 |        5 |            5 | False      |
| 306168 | 412 broccoli casserole               |        40 |         6 |               9 |        5 |            5 | False      |

### Univariate Analysis
<iframe
  src="assets/prep_hist.html"
  width="100%"
  height="400"
  frameborder="0"
></iframe>

The distribution of preparation time shows that most of the data is for recipes that take under 80 minutes, with the most recipes being between the 20 - 40 minute range. With the is_short columnn it makes sense, to divide the data at the 30 minute mark to ensure theres enough data on both sides.

### Bivariate Analysis

<iframe
  src="assets/prep_box.html"
  width="100%"
  height="400"
  frameborder="0"
></iframe>

The distribution of the average rating based on the preparation time ranges shows that there isnt a clear difference between each of the bins because the data seems fairly distributed between all the categories, with the quartiles each being roughly the same. However, in the less than 15 minutes category it does have the largest median at 4.9, but there isn't a clear trend in the other categories.

### Interesting Aggregates


| prep_time_bin   |   avg_rating_mean |   recipe_count |
|:----------------|------------------:|---------------:|
| <15             |           4.71484 |          48431 |
| 15–30           |           4.67837 |          58153 |
| 30–60           |           4.66619 |          69377 |
| 1–2 hr          |           4.67641 |          31817 |
| 2–3 hr          |           4.66468 |           7167 |
| 3+ hrs          |           4.6267  |           8334 |

In the pivot table, focusing on the means of each of the bins, it reinforces that there isn't a clear trend between prep time and the average rating, but less than 15 minutes does have the largest average rating at 4.71 and 3+ hrs does have the smallest average rating at 4.63.

## Assessment of Missingness
### NMAR Analysis
There are four columns with missing data in my merged dataset

| Column      | Missing Values    |
|-----------  |-------------------|
| description | 109               |
| rating      | 14252             |
| review      | 56                |
| avg_rating  | 2626              |

Out of all these columns, the most likely candidate for being NMAR is the 'review' column, because there can be many different reasons why someone would not leave a review for their rating. Unless they are a professional reviewer, unless a recipe is bad or extremely good, many people often will not leave a review for it, instead opting to just rate it out of 5. So here the 'review' column can be left with a lot of blank values seemingly at random but in reality it is because people choose not to leave reviews.

### Missingness Dependency
To now test the dependency of the 'review' column, we look at if the missigness in the 'review' column depends on either the 'minutes' column or the 'n_ingredients' column. To test this, I did permutation tests based on if the absolute value of average rating of the review column subtracted by the no review column for both the 'minutes' and 'n_ingredients' column provided any data that was statistically significant to 0.05.

#### minutes
<iframe
  src="assets/minutes_perm.html"
  width="100%"
  height="400"
  frameborder="0"
></iframe>

For minutes, we see from the graph the red line indicating the test statistic doesn't have too much data to the right of it, and since we're testing the absolute value of the mean difference, we ended up getting a p value of **0.0210** for an observed difference in means of **15.3917**. The p value is less than 0.05 meaning that minutes could play a factor in whether a user leaves a review on a recipe that they rate.

#### n_ingredients
<iframe
  src="assets/ingredients_perm.html"
  width="100%"
  height="400"
  frameborder="0"
></iframe>

For n_ingredients, we see from the graph that the data is more evenly divided between the test statistic, and we end up getting a p value of **0.1470** for an observed difference in means of **0.7183**. The p value is greater than 0.05 meaning that according to the data, the number of ingredients a recipe has, does not affect whether someone would leave a review for a recipe they rated.

## Hypothesis Testing
The question we're exploring is **do short prep time recipes have significantly different average ratings than long prep time recipes?**  To explore this, we do a permutation test measuring the absolute value of the difference between the average rating of recipes that are short vs recipes that are long, using the previously created is_short column.

 - null hypothesis: The average ratings of short prep time and long prep time recipes are the same.  
 - alternative hypothesis: Short-prep recipes have a significantly different average rating than long-prep recipes.

 After running a permutation test, we end up with the following chart:

<iframe
  src="assets/ratings_perm.html"
  width="100%"
  height="400"
  frameborder="0"
></iframe>

We ended up with a p value of 0.0 with an observed difference of 0.0289 between the means of each dataset. This means that we fail to reject the null hypothesis, and there could be a difference between the average ratings or short prep and long prep recipes. This could be because of the ease of making shorter recipes and the perceived lower expectations for those recipes, leaving people to be more happier with the results they can get in those short periods of time.

## Framing a Prediction Problem
After exploring the data, I now aim to try and **predict the average rating of a recipe** only using time data that users would have access too before they rate the recipe. The data columns I will be using are **'n_steps'** and **'time'**, as they both would signal how long a recipe would take which relates to the previous data exploration I have done through this dataset. This is a **regression** problem, as our response variable **'avg_rating'** is a continuous variable from 1-5 as the average could be any decimal number and not necessarily a whole number.

To evaluate the model's effectiveness I looked at the Mean Squared Error (MSE) because it helps penalize the larger errors between the actual and predicted rating to try and find an accurate representation of how accurate the model is.

## Baseline Model
I build a Linear Regression Pipeline in order to properly predict the average rating of the recipe. Both 'n_steps' and 'time' are quantative discrete variables as they are both represented through whole numbers. I did not standardize any of the variables as the values were not categorical.

After I split the model into a training and testing set, I tested out the model and got back an MSE of **0.2448** for the Training Data and **0.2450** for the Testing Data. 

While it is encouraging that the Training and Testing Data both had similar MSE, the numbers are still relatively high, giving a lot of room for some potential improvement.

## Final Model
For my final model, I looked to try and decrease the MSE in a completely new model. Instead of looking at the problem as linear regression, I implemented a **random forest** model, using the previous 'n_steps' and 'minutes', while adding two new data points, 'n_ingredients' and a newly created column 'steps_per_minute' which calculates how long a step takes to complete.

I transformed the 'minutes' data using a QuantileTransformer to normalize the distribution because of how much the data is skewed. The 'steps_per_minute' was transformed using a StandardScaler to better scale the data normally because its based on a ratio.

To find the best model to use, I used GridSearchCV on these hyperparameters: n_estimators, max_depth, and min_samples_leaf, to determine what the optimal values were to ensure the best model. After running GridSearchCV, I found out that the best parameters were  **{'model__max_depth': 20, 'model__min_samples_leaf': 1, 'model__n_estimators': 200}**

With this model, I ended up with a MSE of **0.1988** on the training data and **0.2207** on the testing data, which is a clear improvement upon the previous value, however it creates a bigger gap between the results of the training and testing data.

## Fairness Analysis
To ensure that my model is fair to all groups, it is important to look at how it performs for shorter prep recipes compared to longer prep recipes, and ensure that it is fair in how it analyzes them. In order to test this, I will conduct a permutation test to see how they perform.

This means that my **Group X: Shorter Prep Recipes** and my **Group Y: Longer Prep Recipes**

- Null Hypothesis: Our model is fair. Its precision for shorter preparation recipes and longer preparation recipes are roughly the same, and any differences are due to random chance.
- Alternative Hypothesis: Our model is unfair. Its precision for shorter preperation recipes is different than its precision for longer preparation recipes.
- Test Statistic: Absolute Value Difference between the shorter preparation recipe MSE and longer preparation recipe MSE.
- Significance Level: 0.05

After completing the permutation test, we find that the observed difference in the MSE between the two groups is **0.024** which gives us a **0.01** p value. Since that is less than 0.05, we can reject the null hypothesis, which means there could be a difference between the model's performance between both groups.

It makes sense that there is some variability between how the model performs because the more preparation a recipe needs, leads to generally more ingredients and more variability in the number of steps that it will take, which can skew the model, while shorter recipes are usually more straightforward.