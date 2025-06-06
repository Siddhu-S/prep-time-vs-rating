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
  height="600"
  frameborder="0"
></iframe>

We ended up with a p value of 0.0 with an observed difference of 0.0289 between the means of each dataset. This means that we fail to reject the null hypothesis, and there could be a difference between the average ratings or short prep and long prep recipes. This could be because of the ease of making shorter recipes and the perceived lower expectations for those recipes, leaving people to be more happier with the results they can get in those short periods of time.

## Framing a Prediction Problem





