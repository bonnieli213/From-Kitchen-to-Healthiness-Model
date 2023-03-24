# From Kitchen to Your Health: Recipe And Healthiness
Project for DSC 80 at UCSD \
by Bonnie Li / Nan Huang (b8li@ucsd.edu / n5huang@ucsd.edu)

## Framing the Problem

#### Prediction Problem: 
Predicting the average rating of a recipe according to minutes, nutritions(splitted), n_steps, and n_ingredients. 

#### Type:
Regression

We chose the average rating as the response variable because we wanted to further investigate the relationship between the average rating of each recipe and the other variables. (write what metric we choose and why)
since all the data were generated before the time we extracted them, we knew them at the "time of prediction".

#### Data Cleaning
We removed all brackets from the "nutrition" column and divided the entire nutrition column into 7 separate columns: total fat, sugar, sodium, protein, saturated fat, and carbohydrates. Then, we converted all the nutrition columns from string to float, and dropped all the null values.

|   minutes |   n_steps |   n_ingredients |   calories |   total_fat_(PDV) |   sugar_(PDV) |   sodium_(PDV) |   protein_(PDV) |   saturated_fat_(PDV) |   carbohydrates_(PDV) |   average_rating |
|----------:|----------:|----------------:|-----------:|------------------:|--------------:|---------------:|----------------:|----------------------:|----------------------:|-----------------:|
|        40 |        10 |               9 |      138.4 |                10 |            50 |              3 |               3 |                    19 |                     6 |                4 |
|        45 |        12 |              11 |      595.1 |                46 |           211 |             22 |              13 |                    51 |                    26 |                5 |
|        40 |         6 |               9 |      194.8 |                20 |             6 |             32 |              22 |                    36 |                     3 |                5 |
|       120 |         7 |               7 |      878.3 |                63 |           326 |             13 |              20 |                   123 |                    39 |                5 |
|        90 |        17 |              13 |      267   |                30 |            12 |             12 |              29 |                    48 |                     2 |                5 |


## Baseline Model

For our baseline model, we compared Linear Regression and Decision Tree Regressor model's RMSE and R square. It turns out that the linear regression model has RMSE=0.6397 and R^2=-0.0012, and the decision tree regressor has RMSE=0.6391 and R^2=0.0007. Since Decision Tree Regressor has a lower RMSE and higher R^2, we decided to use Decision Tree Regressor. For features, we used 10 features including minutes,	n_steps, n_ingredients,	calories,	total_fat_(PDV), sugar_(PDV), sodium_(PDV), protein_(PDV), saturated_fat_(PDV), and carbohydrates_(PDV). Minutes,	n_steps, n_ingredients,	and calories are quantitative discrete variables, while total_fat_(PDV), sugar_(PDV), sodium_(PDV), protein_(PDV), saturated_fat_(PDV), and carbohydrates_(PDV) are quantitative continuous variables. We didn't transform any of the features, meaning we use the value as it is. We don't believe our current model is good because we are having a R square of 0.0007, which is too low.



## Final Model
We started from conducting grid search with different options of max_depth and min_samples_split to acquire the optimal hyperparameters. It turns out the best max_depth is 2, and the best min_samples_split is also 2. So, in our pl2 model, we first put all the features in because we believe all these features are related to 'average_rating', which is what we are trying to predict. So, we used optimal hyperparameters and put all the features as is to generate the model. It turns out the pl2 has a R^2 of 0.0021 and RMSE of 0.6387, which is better than our baseline model pl1. 
Conduct GridSearchCV for DecisionTreeRegressor
 we plan to tune max_depth and min_samples_split and our hyperparameters 
 If no maximum depth is specified, the nodes are expanded until all leaves are pure, or 
until all the leaves contain samples smaller than min_samples_split.
We need to choose the best hyperparameters to avoid underfitting or overfitting
A higher min_samples_split value prevents the model from learning the possible height 
Very specific for the particular samples selected for the tree.





### Univariate Analysis

According to the scatter plot, most recipes are found in health levels 1 and 2. Since the number of recipes is decreasing as the health level increases, this trend implies that there are few healthy recipes.

<iframe src="assets/healthiness-level-distribution.html" width=800 height=600 frameBorder=0></iframe>



In the process, we found an outlier with an average rating of 5, and the number of ids was almost half of the total. Since this outlier might affect the shape of the output and the actual result, we took it away. 


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


## Final Model

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


## Fairness Analysis

