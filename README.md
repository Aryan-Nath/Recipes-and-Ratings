# Can I get Seconds? 
An analysis of the cooking time of meals versus their nutritonal content
Author: Aryan Nath

## Introduction
Eating healthy is something that everyone should strive to do. But in today's fast paced world, that is easier said than done. Sometimes we just don't have the time to sit down and cook healthy recipes. But what if that's not true? What if healthy recipes did not take longer to cook, and this was a misconception this whole time? This eventually led me to wonder if we could predict how long a recipe would take based on its nutritional content, changing conceptions around healthy eating and promoting better lifestyles.

I am drawing my data for this project from two datasets consisting of recipes and ratings posted to food.com.

The first dataset is the `Recipes` dataset, with 83782 unique recipes and ten columns with the following information:
| Column             | Description         |
| :------------------| :------------------ |
| `'name'`           | Recipe name         |
| `'id'`             | Recipe ID           |
| `'minutes'`        | The number of minutes it takes to make a particular recipe|
| `'contributor_id'` | Id of the user who posted the recipe |
| `'submitted'`      | Date a recipe was submitted |
| `'tags'`           | Food.com's tags for a recipe |
| `'nutrition'`      | Nutrition information in the form [calories (#), total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV), carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| `'n_steps'`        | Number of steps in recipe |
| `'steps'`          | The written steps to make a particular recipe |
| `'description'`    | User-provided description |
| `'ingredients'`    | Text for recipe ingredients |
| `'n_ingredients'`  | Number of ingredients in recipe|

The second dataset is the `Reviews` dataset, with 731927 rows and each row contains a review from the user on a specific recipe. The columns it includes are:

| Column        | Description         |
| :------------ | :------------------ |
| `'user_id'`   | User ID             |
| `'recipe_id'` | Recipe ID           |
| `'date'`      | Date review was posted |
| `'rating'`    | Rating given        |
| `'review'`    | Review text         |

## Data Cleaning and Exploratory Data Analysis

The next step involved Data Clean-up and preprocessing. I began by left merging the Recipes and Reviews datasets on the `'id'` and `'recipe_id'` columns respectively. This matched each unique recipe with a unique review.

I then checked the data types of all the columns as well as any possible missingness within the data.
 Column             | Description |
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

The only column with missing values was `'description'`, but since it was not relevant to my exploration, the column was dropped.

My next step was to expand the nutrition column of the dataframe. The nutrition column originally contained a list of values correspoding to particular nutrients. I used a lambda function to split this list into the each respective nutrient and then typecast the values as floats to facilitate numerical calculations.

I also added a column to the dataframe called `'Health_Classification'`, which classified a recipe as `'Healthy'` or `'Unhealthy'` based on its nutritional content. The mechanism for this content was defined as follows:
 - Each of the nutrients in a recipe was compared to the median value of that nutrient present in the dataset.
 - For every nutrient except protein, if the nutrient content was greater than the median value, a binary label of 1 was applied and 0 otherwise. For protein, I checked if the recipe's protein content was less than the median amount. This was done because a healthier diet would seek to have a higher protein content and lower caloric, fat, sugar, and sodium content.
 - If a particular recipe had received a score of 1 in 4 or more nutrient columns, it was assigned a tag of `'Unhealthy'`. If it had a score of 3 or less, it was assigned a tag of `'Healthy'`. I chose this metric as I felt it adequately assessed the content of a recipe based on a simple metric a particular recipe's nutritional content compared to other recipes in the dataset.

#### Processed Dataset
| Column                  | Description    |
| :---------------------- | :------------- |
| `'name'`                | object         |
| `'id'`                  | int64          |
| `'minutes'`             | int64          |
| `'contributor_id'`      | int64          |
| `'submitted'`           | datetime64[ns] |
| `'tags'`                | object         |
| `'nutrition'`           | object         |
| `'n_steps'`             | int64          |
| `'steps'`               | object         |
| `'description'`         | object         |
| `'ingredients'`         | object         |
| `'n_ingredients'`       | int64          |
| `'user_id'`             | float64        |
| `'recipe_id'`           | float64        |
| `'date'`                | datetime64 |
| `'rating'`              | float64        |
| `'review'`              | object         |
| `'calories'`        | float64        |
| `'total fat'`     | float64        |
| `sugar'`          | float64        |
| `'sodium'`        | float64        |
| `'protein'`       | float64        |
| `'saturated fat'` | float64        |
| `'carbohydrates'` | float64        |
| `'health_classification'` | bool   |

