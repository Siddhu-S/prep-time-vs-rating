# Do Quick Recipes Really Rate Better? A Data-Driven Look at Recipe Ratings
**Author:** Siddhartha Sarvepally

## Introduction
While in theory it sounds nice to spend hours creating the perfect dish to eat, most of the time that isn't possible. Instead, we are stuck trying to create something edible in the short periods of we have available to us. This is why it is so important to have a variety of quick recipes available to us, to give us a lot of different options to choose from.

In this data science project, the **relationship between preparation time and the average rating** of a recipe is explored, using two datasets from [food.com](food.com), one collecting recipe information, and the other containing rating information all from 2008 - 2018.
<br>

**Dataset #1: Recipes** (83,782 rows)
| Column | Description |
| ----------- | ----------- |
| 'name' | Recipe name |
| 'minutes' | Minutes to prepare recipe |
| 'tags' | Food.com tags for recipe |
| 'nutrition' | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| 'n_steps' | Number of steps in recipe |
| 'n_ingredients' | Number of ingredients in recipe |
<br>

**Dataset #2: Interactions** (731,927 rows)
| Column | Description |
| ----------- | ----------- |
| 'rating' | Rating given |
| 'review' | Review text |

