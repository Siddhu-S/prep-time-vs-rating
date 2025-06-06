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

After fully cleaning the data, the merged dataframe looks as follows:
| name                                 |   minutes |   n_steps |   n_ingredients |   rating |   avg_rating | is_short   |
|:-------------------------------------|----------:|----------:|----------------:|---------:|-------------:|:-----------|
| 1 brownies in the world    best ever |        40 |        10 |               9 |        4 |            4 | False      |
| 1 in canada chocolate chip cookies   |        45 |        12 |              11 |        5 |            5 | False      |
| 412 broccoli casserole               |        40 |         6 |               9 |        5 |            5 | False      |
| 412 broccoli casserole               |        40 |         6 |               9 |        5 |            5 | False      |
| 412 broccoli casserole               |        40 |         6 |               9 |        5 |            5 | False      |

### Exploratory Data Analysis
<iframe
  src="assets/prep_hist.html"
  width="100%"
  height="400"
  frameborder="0"
></iframe>

The distribution of preparation time shows that most of the data is for recipes that take under 80 minutes, with the most recipes being between the 20 - 40 minute range. With the is_short columnn it makes sense, to divide the data at the 30 minute mark to ensure theres enough data on both sides.

<iframe
  src="assets/prep_box.html"
  width="100%"
  height="400"
  frameborder="0"
></iframe>

The distribution of the average rating based on the preparation time ranges shows that there isnt a clear difference between each of the bins because the data seems fairly distributed between all the categories, with the quartiles each being roughly the same. However, in the less than 15 minutes category it does have the largest median at 4.9, but there isn't a clear trend in the other categories.


| prep_time_bin   |   avg_rating_mean |   recipe_count |
|:----------------|------------------:|---------------:|
| <15             |           4.71484 |          48431 |
| 15–30           |           4.67837 |          58153 |
| 30–60           |           4.66619 |          69377 |
| 1–2 hr          |           4.67641 |          31817 |
| 2–3 hr          |           4.66468 |           7167 |
| 3+ hrs          |           4.6267  |           8334 |

In the pivot table, focusing on the means of each of the bins, it reinforces that there isn't a clear trend between prep time and the average rating, but less than 15 minutes does have the largest average rating at 4.71 and 3+ hrs does have the smallest average rating at 4.63.
