# Can I get Seconds? 
An analysis of the cooking time of meals versus their nutritional content.

Author: Aryan Nath

## Introduction
Eating healthy is something that everyone should strive to do. But in today's fast paced world, that is easier said than done. Sometimes people just don't have the time to sit down and cook healthy recipes. But what if that's not true? What if healthy recipes did not take longer to cook, and this was a misconception this whole time? This eventually led me to wonder if I could predict how long a recipe would take based on its nutritional content, changing conceptions around healthy eating and promoting better lifestyles.

I am drawing my data for this project from two datasets consisting of recipes and ratings posted to food.com.

The first dataset is the `Recipes` dataset, with 83782 unique recipes and ten columns with the following information:

| Column                | Description                                                                                                   |
|:----------------------|:--------------------------------------------------------------------------------------------------------------|
| name                  | Recipe name                                                                                                   |
| id                    | Recipe ID                                                                                                     |
| minutes               | The number of minutes it takes to make a particular recipe                                                    |
| contributor_id        | Id of the user who posted the recipe                                                                          |
| submitted             | Date a recipe was submitted                                                                                   |
| tags                  | Food.com's tags for a recipe                                                                                  |
| nutrition             | Nutrition information in the form [calories, total fat, sugar, sodium, protein, saturated fat, carbohydrates] |
| n_steps               | Number of steps in recipe                                                                                     |
| steps                 | The written steps to make a particular recipe                                                                 |
| description           | User-provided description                                                                                     |
| ingredients           | Text for recipe ingredients                                                                                   |
| n_ingredients         | Number of ingredients in recipe                                                                               |
| user_id               | User ID                                                                                                       |
| recipe_id             | Recipe ID                                                                                                     |
| date                  | Date review was posted                                                                                        |
| rating                | Rating given                                                                                                  |
| review                | Review text                                                                                                   |
| calories              | Calories                                                                                                      |
| total fat             | Total Fat                                                                                                     |
| sugar                 | Sugar                                                                                                         |
| sodium                | Sodium                                                                                                        |
| protein               | Protein                                                                                                       |
| saturated fat         | Saturated Fat                                                                                                 |
| carbohydrates         | Carbohydrates                                                                                                 |
| health_classification | Health classification                                                                                         |

The second dataset is the `Reviews` dataset, with 731927 rows and each row contains a review from the user on a specific recipe. The columns it includes are:

| Column      | Description            |
|:------------|:-----------------------|
| `user_id`   | User ID                |
| `recipe_id` | Recipe ID              |
| `date`      | Date review was posted |
| `rating`    | Rating given           |
| `review`    | Review text            |


## Data Cleaning and Exploratory Data Analysis

The next step involved Data Clean-up and preprocessing. I began by left merging the Recipes and Reviews datasets on the `'id'` and `'recipe_id'` columns respectively. This matched each unique recipe with a unique review.

I then checked the data types of all the columns as well as any possible missingness within the data.
 
| Column             | Description |
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

| Column                  | Dtype          |
|:------------------------|:---------------|
| `name`                  | object         |
| `id`                    | int64          |
| `minutes`               | int64          |
| `contributor_id`        | int64          |
| `submitted`             | datetime64     |
| `tags`                  | object         |
| `nutrition`             | object         |
| `n_steps`               | int64          |
| `steps`                 | object         |
| `description`           | object         |
| `ingredients`           | object         |
| `n_ingredients`         | int64          |
| `user_id`               | float64        |
| `recipe_id`             | float64        |
| `date`                  | datetime64     |
| `rating`                | float64        |
| `review`                | object         |
| `calories`              | float64        |
| `total fat`             | float64        |
| `sugar`                 | float64        |
| `sodium`                | float64        |
| `protein`               | float64        |
| `saturated fat`         | float64        |
| `carbohydrates`         | float64        |
| `health_classification` | bool           |

The final cleaned dataframe has 78906 rows and 21 columns. Here are the first five rows of the cleaned dataframe with the columns that I felt contained relevant content (since the dataframe has a large number of columns it was not possible to include them all for display).

|    | name                                 |     id |   minutes |   n_steps |   n_ingredients |   calories |   protein |   total fat | health_classification   |
|---:|:-------------------------------------|-------:|----------:|----------:|----------------:|-----------:|----------:|------------:|:------------------------|
|  0 | 1 brownies in the world    best ever | 333281 |        40 |        10 |               9 |      138.4 |         3 |          10 | Healthy                 |
|  1 | 1 in canada chocolate chip cookies   | 453467 |        45 |        12 |              11 |      595.1 |        13 |          46 | Unhealthy               |
|  2 | 412 broccoli casserole               | 306168 |        40 |         6 |               9 |      194.8 |        22 |          20 | Healthy                 |
|  3 | millionaire pound cake               | 286009 |       120 |         7 |               7 |      878.3 |        20 |          63 | Unhealthy               |
|  4 | 2000 meatloaf                        | 475785 |        90 |        17 |              13 |      267   |        29 |          30 | Healthy                 |

### Univariate Analysis
I have decided to examine the distribution of recipe cooking times on food.com. As can be seen from the plot, the distribution of cooking times is right skewed, with the number of recipes decreasing as the cooking time increases.

<iframe
  src="assets/distribution-of-minutes.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Bivariate Analysis
For this analysis, I have looked at the distrbution of cooking time vs the number of steps in a recipe. I chose this plot as I believed recipes with more steps would take more time due to their increased complexity. As can be seen from the plot however, the recipes are pretty evenly spread out, with a relatively weak correlation of 0.32 between the number of steps and the time taken.

<iframe
  src="assets/distribution-of-minutes-vs-steps.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

### Interesting Aggregates
I chose to investigate the trends in nutritional content as the cooking time of recipes increased. I created a dataframe that stored cleaned nutritional content values, with outliers defined to be outside of the 99th quantile. The nutrients were then aggreagated by mean and median and displayed in the pivot table below.

| minutes   | calories_mean   | calories_median   | total fat_mean   | total fat_median   | protein_mean   | protein_median   |
|:----------|:----------------|:------------------|:-----------------|:-------------------|:---------------|:-----------------|
| 0         | 653.1           | 653.1             | 46.0             | 46.0               | 118.0          | 118.0            |
| 1         | 174.955         | 133.9             | 7.78603          | 0.0                | 5.82533        | 0.0              |
| 2         | 205.851         | 144.9             | 9.0884           | 0.0                | 6.82873        | 1.0              |
| 3         | 244.072         | 169.35            | 11.7562          | 2.0                | 8.07231        | 2.0              |
| 4         | 294.923         | 205.45            | 20.5489          | 7.0                | 14.688         | 9.0              |
| 5         | 269.465         | 172.3             | 19.9864          | 6.0                | 10.8451        | 4.0              |
| 6         | 267.396         | 178.25            | 18.8476          | 7.0                | 14.3994        | 7.0              |
| 7         | 312.889         | 227.6             | 23.6853          | 12.0               | 19.1878        | 13.0             |
| 8         | 295.253         | 208.3             | 22.8502          | 13.0               | 18.1223        | 11.0             |
| 9         | 298.191         | 231.45            | 20.4364          | 16.0               | 24.3455        | 18.5             |
| 10        | 296.825         | 199.95            | 24.886           | 13.0               | 15.7661        | 8.0              |
| ...       | ...             | ...               | ...              | ...                | ...            | ...              |
| 245       | 413.658         | 270.05            | 26.217           | 15.0               | 43.3491        | 31.0             |
| 247       | 247.8           | 247.8             | 13.5             | 13.5               | 31.5           | 31.5             |
| 248       | 525.85          | 370.1             | 39.1667          | 31.0               | 48.6667        | 40.0             |
| 250       | 395.164         | 311.1             | 27.0734          | 15.5               | 39.4908        | 31.0             |

As can be observed from the graphs below, the mean and median cooking times fluctuate a lot as the amount of cook time increases. However, it should be noted that the shapes of the mean and median curves appear to follow each other, suggesting a relation between the two.

<iframe
  src="assets/calories_trend_line_chart.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/protein_trend_line_chart.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

<iframe
  src="assets/total fat_trend_line_chart.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

## Assessment of Missingness
When I checked the datasets at the beginning for missing values, there was only one column in each dataset that had missing values. The `'Recipes'` dataset had 70 missing values in the `'description'` column and the `'Reviews'` dataset had 169 missing values in the `'review'` column. These missing values only occur in columns that contain textual and descriptive content, which I am not planning on using in my research. Since my prediction is not related to these columns, I have chosen to drop them as I continue working.

### NMAR Analysis

I believe the missingness in the 'review' column is likely Not Missing At Random (NMAR). The decision for a user to omit written review text is likely dependent on the actual, unobserved level of satisfaction or dissatisfaction that is not captured by the provided features. For example:
- A user might give a perfect rating (5) but choose not to write a review because they were completely satisfied and saw no need for elaboration.
- Conversely, a user might give a very low rating (1) and omit a review because they were so frustrated they stopped engaging with the system.

This unobserved psychological reason for omitting the text is not present in my dataset, making the missingness NMAR. If I were to obtain additional data, such as a survey asking users why they chose not to write a review, I could potentially convert this to a MAR scenario.

### Missingness Dependency
To determine if the missingness of the 'review' column is dependent on an observed feature (Rating Extremity), I conducted a permutation test. This test checks if the missingness of the review text is different between recipes with an extreme rating (1 or 5) and those with a mid-range rating (2, 3, or 4).

**Groups**: Extreme Ratings (1 or 5) vs. Mid-Range Ratings (2, 3, or 4).

**Null Hypothesis**: The probability for a 'review' to be missing is independent of rating extremity.

**Alternative Hypothesis**: The probability for a 'review' to be missing is dependent on rating extremity.

**Test Statistic**: Difference in Proportion of Missing Reviews (Extreme Ratings - Mid-Range Ratings).

**Significance Level**: 0.05

<iframe
src="assets/missingness_permutation_plot.html"
width="800"
height="600"
frameborder="0"
></iframe>

**Observed Statistic**: 0.0001 (Prop Missing Extreme: 0.0003, Prop Missing Mid-Range: 0.0002)

**P-value**: 0.38

#### Conclusion
Since the calculated p-value of 0.38 is greater than the significance level of 0.05, I fail to reject the null hypothesis. I do not have sufficient evidence to conclude that the missingness of the 'review' column is dependent on the rating extremity.


## Hypothesis Testing

The topic of interest for my research is whether healthy and unhealthy recipes take the same or different amounts of time to cook. These values are defined in the `'health_classification'` column, which assigns binary tags to a recipe based on predefined criteria.

To further investigate this question, I chose to run a **Permutation Test**.

**Null Hypothesis**: Mean time of Healthy recipes = Mean time of Unhealthy recipes.

**Alternate Hypothesis**: Mean time of Healthy recipes < Mean time of Unhealthy recipes.

**Test Statistic**: Difference in Means (Unhealthy - Healthy)

**Significance Level**: 0.05

**Observed Test Statistic**: 12.827 minutes

I chose to use a Permutation Test as I wanted to see if the two types of recipes could have possibly come from the same population. My alternative hypothesis was that Healthy Recipes actually took less time to make than Unhealthy Recipes, which meant people should be more inclined to make Healthy Recipes. The chosen test statistic was the difference in mean cooking times of Healthy and Unhealthy Recipes. Before I began the permutation test, I calculated the observed test statistic, which showed that Healthy Recipes took 12.8 minutes less to cook on average than Unhealthy Recipes. After that, I shuffled the `'health_classification'` labels 5000 times to generate 5000 differences in means between Health and Unhealthy Recipes. If the null hypothesis were true, the expected difference in times would be centered around 0.

<iframe
  src="assets/hypothesis-test.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

**Calculated P-value**: 0.0

After shuffling the labels, I calculated the p-value of our statistic to be 0. Since the p-value is less than 0.05, **we reject the null hypothesis**. Healthy Recipes turn out to take less time to cook than Unhealthy Recipes.

## Framing a Prediction Problem

The overall goal of my project is to investigate the misconception that healthy recipes require more time to cook. To address this, I will build a model that predicts the time complexity of a recipe based only on its observable nutrient content and simple structural features.

**Prediction Problem**: Given a recipe's nutritional profile and simple structural features, I plan to predict which of ten time complexity groups (deciles) the recipe's total preparation time falls into.

**Type**: Multi-class Classification. I am performing multi-class classification because my target variable, representing cooking time, has been discretized into 10 distinct, ordered categories (deciles), which the model must predict.

**Response Variable**: The continuous 'minutes' column was transformed using `log(1 + minutes)` and then discretized into a categorical target called `'time_classification'`. This target consists of 10 decile classes (labeled 0 through 9). I chose this variable because it transforms the highly skewed continuous time into a balanced, ordinal classification problem, making the task feasible for the models.

**Evaluation Metric**: I chose the Weighted F1-Score as the primary evaluation metric.

**Justification for F1-Score**:
I will use the F1-Score because the original distribution of cooking times is extremely skewed. Although the quantile binning makes the classes balanced by support, the consequences of misclassification are not equal across all 10 categories (e.g., misclassifying a very long recipe is worse than misclassifying a short one). The Weighted F1-Score is robust for this multi-class task, as it provides a balanced measure of precision and recall across all 10 time categories, preventing the model from simply optimizing for overall correct guesses.

**Features**:
- Nutritional Features (7): `'calories'`, `'total fat'`, `'saturated fat'`, `'carbohydrates'`, `'protein'`, `'sugar'`, `'sodium'`.
- Other Features (2): `'n_steps'` and `'n_ingredients'`.

## Baseline Model

For my Baseline Model, I trained a Random Classifier model using `'n_steps'` and `'n_ingredients'` as the features. The data was split into training and test sets, and I evaluated its performance based on the model's F1 score.

The Baseline Model had a Weighted F1 Score of 0.1792. Unfortunately, this low score proves that the model was not able to accurately predict the cooking time based on the present features.

## Final Model

For the final mode, I incorporated all of the nutritional information features in addition to the two features that I was using in the Baseline Model. Due to the right skew in the target minutes column as well as the nutritional information columns, I implemented a log transformation on the features. I used three types of modelling algorithms, namely Random Forests, Gradient Boosting, and KNeighbors, and implemented `GridSearchCV` to tune the hyperparameters of the models (`classifier_max_depth`, `selector_k`, and `classifier_n_estimators`). I chose to tune the hyperparameters to avoid overfitting to the training set and ensure that the model generalizes well to unseen data.

The best performing model was the Random Forest, with a Weighted F1 Score of 0.2303. This was an improvement of 0.0511 in F1 Score, showing that the added complexity made the model more adept at predicting cooking times than the Baseline Model. The associated hyperparameters that optimized this model were as follows: `classifier__max_depth`: 25, `classifier__n_estimators`: 200, `selector__k`: 9.

## Fairness Analysis

For the fairness analysis, I investigated whether the model performs differently based on recipe complexity, which was measured using the `'n_steps'` feature.

I split the recipes into two groups:
- Group X (Low Complexity): Recipes with n_steps <= 9 steps.
- Group Y (High Complexity): Recipes with n_steps > 9 steps.

I found that the median number of steps for my dataset is 9, which I chose as the binarization threshold.

I chose to evaluate the model using the Weighted F1-Score Parity because the prediction task is a multi-class classification problem (10 quantiles). The F1-Score provides a balanced measure of both precision and recall across all 10 time categories. I hypothesized that my model would struggle more with High Complexity recipes, as their preparation logic may be more diverse and harder to map accurately to a time quantile.

**Null Hypothesis**: My model is fair. Its Weighted F1-Score for Low Complexity recipes and High Complexity recipes are roughly the same, and any observed difference is due to random chance.

**Alternative Hypothesis**: My model is unfair. Its Weighted F1-Score for Low Complexity recipes is higher than its score for High Complexity recipes.

**Test Statistic**: Difference in Weighted F1-Score
**Significance Level**: 0.05
**Number of Permutations**: 1000

To run the permutation test, I first calculated the observed F1-Scores for the two groups using the best fitted model from Step 7:
- F1-Score (Low Complexity): 0.2587
- F1-Score (High Complexity): 0.1869
- Observed Test Statistic (Difference): 0.0718

I then shuffled the group labels (`'is_low_complexity'`) 1000 times to collect 1000 simulated differences, modeling the null distribution.

<iframe
  src="assets/fairness_permutation_test_visualization.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

After running my permutation test, I obtained a p-value of 0.0.

#### Conclusion:
Since the calculated p-value of 0.0 is less than the significance level of 0.05, **I reject the null hypothesis**. The model's performance (F1-Score) is statistically significantly higher for Low Complexity recipes than for High Complexity recipes. This confirms my hypothesis that recipes requiring higher preparation complexity are more difficult for the model to classify accurately into the correct cooking time quantile.
