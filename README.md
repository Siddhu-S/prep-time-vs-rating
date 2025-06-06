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
| minutes        | Minutes to prepare recipe                                                   |
| tags           | Food.com tags for recipe                                                    |
| nutrition      | Nutrition info: [calories, total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)] |
| n_steps        | Number of steps in recipe                                                   |
| n_ingredients  | Number of ingredients in recipe                                             |

**Dataset #2: Interactions** (731,927 rows)

| Column | Description       |
|--------|-------------------|
| rating | Rating given      |
| review | Review text       |


With the findings from this project, we can have a better insight into what factors make a recipe more popular (in this case having a higher rating), and it can better inform a food.com user on what recipes, their audience would respond better too.

## Data Cleaning and Exploratory Data Analysis
### Data Cleaning
Before starting exploration into the data, we cleaned the data using the following steps in order to combine it all into one dataset and ensure we dealt with any null values or data type errors.

1. Left merge the recipes and interactions dataset on 'id' and 'recipe_id' in order to ensure all recipes stayed in the merged dataset irrespective of whether they have any reviews in the interactions dataset.

2. Replace all the 0s in the ratings column with NaN to ensure they do not skew the analysis, and then use a groupby function to find the average rating of each recipe.

3. Convert data in the 'submitted' column to datetime to make for easier parsing during exploratory data analysis

4. Filter the data, removing all recipes that took 0 minutes or less and recipes that were over 360 minutes or 6 hrs. There were many outliers in the dataset, with some recipes taking multiple weeks, and when looking into relationship between preparation time and average rating, it makes the most sense to look at a generalized dataset of up to six hours which is the general max threshold for the time someone would take to create one meal.

5. Create column 'is_short' to be used in later analysis, gives a boolean value depending on if the prep time for the recipe was less than or equal to 30 minutes

### Exploratory Data Analysis
<iframe
  src="assets/prep_hist.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The distribution of preparation time shows that most of the data is for recipes that take under 80 minutes, with the most recipes being between the 20 - 40 minute range. With the is_short columnn it makes sense, to divide the data at the 30 minute mark to ensure theres enough data on both sides.

<iframe
  src="assets/prep_box.html"
  width="100%"
  height="400"
  frameborder="0"
></iframe>

The distribution of the average rating based on the preparation time ranges shows a general trend of the average rating of each bin going down as more preparation time is added in, although the average rating data is very concentrated between 4-5, showing most of the data is good ratings which could skew the analysis.