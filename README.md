# From Kitchen to Your Health: Recipe And Healthiness
Project for DSC 80 at UCSD \
by Bonnie Li / Nan Huang (b8li@ucsd.edu / n5huang@ucsd.edu)

## Framing the Problem

#### Prediction Problem: 
Predicting the average rating of a recipe according to minutes, nutritions(splitted), n_steps, and n_ingredients. 

#### Type:
Regression

We chose the average rating as the response variable because we wanted to further investigate the relationship between the average rating of each recipe and the other variables. We are using RMSE and R^2 as our metrics because both R squared and RMSE provide valuable information about the performance of a decision tree regressor. R squared can indicate how well the model fits the data overall, while RMSE provides information about the magnitude of errors. By considering both metrics, we can gain a better understanding of how well the model is performing and identify areas for improvement.
Since all the data were generated before the time we extracted them, we knew them at the "time of prediction".

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
We started from conducting grid search with different options of max_depth and min_samples_split to acquire the optimal hyperparameters. It turns out the best max_depth is 2, and the best min_samples_split is also 2. So, in our pl2 model, we first put all the features in because we believe all these features are related to 'average_rating', which is what we are trying to predict. So, we used optimal hyperparameters and put all the features as is to generate the model. It turns out the model has a R^2 of 0.0021 and RMSE of 0.6387, which is better than our baseline model. 

However, we still want to improve our model because the R^2 is too low. So, we decided to tranform some of the columns. 

First, after observing the distribution for sugar_(PDV), we found out their relationship with average_rating may be: average_rating = 1/ sugar_(PDV): 

<iframe src="assets/healthiness-level-distribution.html" width=800 height=600 frameBorder=0></iframe>

Therefore we decided to apply np.sqrt to it. So does 'calories','total_fat_(PDV)', 'sodium_(PDV)','protein_(PDV)','saturated_fat_(PDV)','carbohydrates_(PDV)'.

Second, We double 'n_steps' and 'minutes' because we believe that when "n_steps" and "minutes" are larger, the recipe is more complex, so its average rating is worse, so we want to double these two features to add their weights. 


## Fairness Analysis

