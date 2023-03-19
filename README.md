# From-Kitchen-to-Healthiness-Model
DSC80 Project 5
# From Kitchen to Your Health: Recipe And Healthiness
Project for DSC 80 at UCSD \
by Bonnie Li / Nan Huang (b8li@ucsd.edu / n5huang@ucsd.edu)

## Introduction
In this project, we are going to investigate a dataset containing recipes and ratings from food.com. After extracting two raw datasets, one containing recipes and one containing reviews and ratings submitted for the recipes in the first dataset, we merged them and calculated the average ratings for each recipe. Before calculating the average, we replace all the '0' ratings with 'np.nan' to get the accurate average rating. If we don't replace them with 0, those recipe without a rating will not be included. The first row of the resulting dataset will be:

| name                                 |     id |   minutes |   contributor_id | submitted   | tags                                                | nutrition                                |   n_steps | steps                                               | description                                       | ingredients                                         |   n_ingredients |   average_rating |
|:-------------------------------------|-------:|----------:|-----------------:|:------------|:----------------------------------------------------|:-----------------------------------------|----------:|:----------------------------------------------------|:--------------------------------------------------|:----------------------------------------------------|----------------:|-----------------:|
| 1 brownies in the world    best ever | 333281 |        40 |           985201 | 2008-10-27  | ['60-minutes-or-less', 'time-to-make', 'course...'] | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0] |        10 | ['heat the oven to 350f and arrange the rack i...'] | these are the most; chocolatey, moist, rich, d... | ['bittersweet chocolate', 'unsalted butter', '...'] |               9 |                4 |

We will investigate the relationship between the level of healthiness and the average rating of recipes. If there is a relationship, the reader can quickly decide whether a recipe is healthy based on the rating. If there is no relationship, the reader can ignore the rating when trying to pick a healthy recipe. 

Extract the relevant information from the merged dataset above, we have a dataset with 83782 rows and 4 columns, 'name', 'id', 'nutrition', and 'average_rating', as example below: 

| name                                 |     id | nutrition                                    |   average_rating |
|:-------------------------------------|-------:|:---------------------------------------------|-----------------:|
| 1 brownies in the world    best ever | 333281 | [138.4, 10.0, 50.0, 3.0, 3.0, 19.0, 6.0]     |                4 |
| 1 in canada chocolate chip cookies   | 453467 | [595.1, 46.0, 211.0, 22.0, 13.0, 51.0, 26.0] |                5 |
| 412 broccoli casserole               | 306168 | [194.8, 20.0, 6.0, 32.0, 22.0, 36.0, 3.0]    |                5 |


A description of each column in both datasets is given below.

| Column         | Description                                                                                                                                                                                                                               |
|:---------------|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| name           | Recipe name                                                                                                                                                                                                                               |
| id             | Recipe ID                                                                                                                                                                                                                                 |
| nutrition      | Nutrition information in the form [calories (#),                     total fat (PDV), sugar (PDV), sodium (PDV), protein (PDV), saturated fat (PDV),                     carbohydrates (PDV)]; PDV stands for “percentage of daily value” |
| average_rating | Average rating of each recipe                                                                                                                                                                                                            |


## Cleaning and EDA
### Data Cleaning

First, we removed all brackets from the "nutrition" column and divided the entire nutrition column into 7 separate columns corresponding to calories, total fat, sugar, sodium, protein, saturated fat, and carbohydrates. Then, we converted all the nutrition columns from string to float.

Since we wanted to examine the relationship between the health level and the average rating of the recipes, we wanted to assign a health level to each recipe based on each nutritional data. In general, the recommended daily calorie intake is 2000 calories for women and 2500 calories for men, so the healthy meal we defined should be below 833 calories. The pdv for each nutrient should be less than 33%. If the nutritional data is within this range, we will give it a True, otherwise False. By adding up the individual nutritional columns, we will get a new column called "healthiness level", ranging from 0 to 7 to represent the level of healthiness for each recipe.

| name                                 |     id |   average_rating | calories   | total_fat_(PDV)   | sugar_(PDV)   | sodium_(PDV)   | protein_(PDV)   | saturated_fat_(PDV)   | carbohydrates_(PDV)   |   healthiness level |
|:-------------------------------------|-------:|-----------------:|:-----------|:------------------|:--------------|:---------------|:----------------|:----------------------|:----------------------|--------------------:|
| 1 brownies in the world    best ever | 333281 |                4 | False      | False             | True          | False          | False           | False                 | False                 |                   1 |
| 1 in canada chocolate chip cookies   | 453467 |                5 | False      | True              | True          | False          | False           | True                  | False                 |                   3 |
| 412 broccoli casserole               | 306168 |                5 | False      | False             | False         | False          | False           | True                  | False                 |                   1 |


### Univariate Analysis

According to the scatter plot, most recipes are found in health levels 1 and 2. Since the number of recipes is decreasing as the health level increases, this trend implies that there are few healthy recipes.

<iframe src="assets/healthiness-level-distribution.html" width=800 height=600 frameBorder=0></iframe>



In the process, we found an outlier with an average rating of 5, and the number of ids was almost half of the total. Since this outlier might affect the shape of the output and the actual result, we took it away. 

|   average_rating |   count |
|-----------------:|--------:|
|          4.97368 |       1 |
|          4.97727 |       1 |
|          4.98113 |       1 |
|          4.99078 |       1 |
|          5       |   47784 |

According to the distribution plot for the average rating of less than 5, the id counts are concentrated in cases with an average rating greater than 3.
<iframe src="assets/average-rating-distribution.html" width=800 height=600 frameBorder=0></iframe>


### Bivariate Analysis

In the scatter plot, the points are very dispersed. However, the regression line shows a weak positive correlation between the average rating and the level of healthiness. The plot implies that the average rating increases as the healthiness of the food increases. 

<iframe src="assets/healthiness-hevel-vs-average-rating-scatter.html" width=800 height=600 frameBorder=0></iframe>

Since the histograms we created were uniform, the average score for each health level was averaged. This finding supports the scatterplot we drew above. The correlation between the health level and the average score is very ambiguous.

<iframe src="assets/healthiness-level-vs-proportional-average-rating.html" width=800 height=600 frameBorder=0></iframe>

### Interesting Aggregates

The pivot table we generated allows us to see that the proportion of average rating for each healthiness level is evenly distributed, which is consistent with the histogram we plotted earlier. Therefore, there is a large uncertainty in the correlation between health levels and average rating.

|   healthiness level |   average_rating |
|--------------------:|-----------------:|
|                   0 |         0.125079 |
|                   1 |         0.124875 |
|                   2 |         0.124833 |
|                   3 |         0.125232 |
|                   4 |         0.124865 |
|                   5 |         0.124584 |
|                   6 |         0.125506 |
|                   7 |         0.125025 |


## Assessment of Missingness
### NMAR Analysis

According to the table below, 'name', 'description' and 'average_rating' have missing data.

| columns        |    0 |
|:---------------|-----:|
| name           |    1 |
| id             |    0 |
| minutes        |    0 |
| contributor_id |    0 |
| submitted      |    0 |
| tags           |    0 |
| nutrition      |    0 |
| n_steps        |    0 |
| steps          |    0 |
| description    |   70 |
| ingredients    |    0 |
| n_ingredients  |    0 |
| average_rating | 2609 |

We consider the 'description' to be the NMAR. Recipe creators can choose not to write a description if they feel it is not necessary.

### Missingness Dependency

Since 'description' is NMAR, we will test the missingness of 'average_rating' column. We believe 'average_rating' is MAR, since it dependent on 'submitted', but not dependent on 'n_ingredients'.

#### average_rating vs submitted

We classify the submissions into two types: early and late. dates before 2013-06-18 are defined as early and dates after 2013-06-18 are defined as late. We want to test whether the average rating is MAR dependent on early and late submissions.

In the table and bar chart below, we show the distribution of early, denoted as True, and late, denoted as False, is very different. 

| submitted   |   rating_missing = False |   rating_missing = True |
|:------------|-------------------------:|------------------------:|
| False       |                0.0457172 |                0.118053 |
| True        |                0.954283  |                0.881947 |

<iframe src="assets/Submitted-Time-by-Missingness-of-Average-Rating.html" width=800 height=600 frameBorder=0></iframe>

Using TVD as test statistics, we get an observed TVD of 0.07233. Then we ran a permutation test with 1000 repetitions, we got a p-value of 0.0 whcih is smaller than 0.01 significance level, so we reject the null.  

<iframe src="assets/Empirical-Distribution-of-the-TVD.html" width=800 height=600 frameBorder=0></iframe>

Since the null stated that the distribution of 'submitted' when 'rating' is missing is the same as the distribution of 'submitted' when 'rating' is not missing. Hence, we conclude that the missingness in the 'rating' column is dependent on 'submitted'.

#### average_rating vs n_ingredients

In the box plot and histogram below, we show the distribution of the number of components corresponding to the missingness of the average rating. The distribution of the average rating's missingness is True, and missingness is False is highly overlapping.

<iframe src="assets/n_ingredients-by-Missingness-of-Average-Rating-hist.html" width=800 height=600 frameBorder=0></iframe>
<iframe src="assets/n_ingredients-by-Missingness-of-Average-Rating-line.html" width=800 height=600 frameBorder=0></iframe>

Also, since the absolute difference between the two group means is 0.2542, which is very small, we decided to use KS as the test statistic to run our permutation test further. 

Since we obtained a p-value of 0.0132, which is greater than 0.01 significance level, we fail to reject the null hypothesis. The null hypothesis states that the distribution of 'n_ingredients' when 'average_rating' is missing is the same as the distribution of 'n_ingredients' when 'average_rating' is not missing. Therefore, we conclude that the missingness of the 'average_rating' column is not dependent on 'n_ingredients'.


## Hypothesis Testing
Again, our question is: what is the relationship between the level of healthiness and average rating of recipes?

Null Hypotheses: The level of healthiness and average rating come from the same distribution. \
Alternative Hypotheses: The level of healthiness and average rating do not come from the same distribution. 

Since we only have samples, but no information about any population distributions, we are going to conduct a permutation test. \
Since the dataset is extensive, more confounding variables may affect the result. We need to use a more strict threshold as our significance level. So we use 0.01 as the significance level, representing higher statistical significance.\
We consider each healthiness level as one group. Since the average ratings for each group are quantitative, we will choose the absolute differences in group means as the test statistic.

The observed differences in the group mean we obtained is 0.342. We ran a permutation test with 1000 repetitions and got a p-value of 0.299, which is much larger than the significance level. We fail to reject the null. Hence, we conclude that the healthiness and average rating may come from the same distribution. As the plot shows below:

<iframe src="assets/Empirical-Distribution-of-the-Absolute-Differences-in-Group-Means.html" width=800 height=600 frameBorder=0></iframe>
