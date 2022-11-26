# Train-Delay-Pred

# Project Overview
CSV file

**Packages**: pandas, numpy, sklearn, matplotlib, seaborn, streamlit

## **1.  Exploratoy Data Analysis (EDA)**

*  Loading data
*  Statistics of the data frame
*  Data visualization
*  Data Cleaning
*  Fixing formats

## Data Visualization
Histogram and Box Plots show that there are many outliers in the continous features.

### Data Cleaning (Missing values and formats)
Missing values were handled with imputation based on some rules that were present in the dataset. For example, some features presented the same value of another feature.

## **2.  Feature Engineering**
New features were created:

* rush_hour: Binary feature, it's equal to 1 if the train is traveling within a given rush hour. 0 otherwise.
* difference: Difference between delay at arrival and delay at departure.
* same_line: Binary feature, it's equal to 1 if the train arrives at the same line that departed. 0 otherwise.

**Outlier detection**
Outliers were detected and managed with a custom function to deal with them. The function calculates the Z score to detect if the given record is an outlier, then, the function calculates the mean of the records that were not marked as outliers, and finally, outliers are replaced with the calculated mean.

## **3. Modeling and Evaluation**
Because of the number of categories within every categorical feature, the total amount of features was 239. A first approach to modeling was using Principal Component Analysis (PCA) to reduce dimensionality and gain computer time performance, however, model performance was not better. The next table shows the comparison between regular approach Vs PCA approach for differente models:

Before moving forward, it is important to explore how the models perform in the train set without cross-validation to determine if the models present overfitting or underfitting. As it's shown in the next table, the RMSE in train-set are relatively normal, neither not too low (overfitting) nor too high (underfitting). On the other hand, the RMSE with Cross Validation is on average 30 seconds higher, which shows that models are behaving well

Hyperparameter tuning was performed with Randomized Grid Search, but performance didn't improve.

## **Model Evaluation**
It's time to evaluate the model in the test set and discover if the model works well to predict unseen data. The next figure shows how good is the model:

<img src= "https://github.com/jsroa15/Train-delay-prediction/blob/main/images/modeleval.jpg?raw=true" width="800"/>

The RMSE in the test set is 195 Seconds, which represents an increase of 104.1 seconds, showing that the model's performance in the test-set is not the best, and the model maybe is facing overfitting or another issue with modeling or data.

One of the previous steps before the modeling phase was to replace outliers in the train-set, but don't in the test set because is data that is supposed to be unknown. Exploring the shape of the continuous features in the test-set can give a better understanding:

<img src= "https://github.com/jsroa15/Train-delay-prediction/blob/main/images/testeda.jpg" width="800"/>

The figure shows that there are many outliers in the continuous features in the test-set, which can cause the model is not working well, because the model was trained without that behavior. At this point is impossible to decide if outliers are related whether to errors in data collection or normal conditions in the railway operation.

Based on the previous statement it's possible to take advantage of another feature of XGBoost models: they are robust to outliers, for that reason it's feasible to model that behavior to determine if the performance can improve.

Applying the same methodology, the model will be tested in train-set, cross-validation, and the test-set. Additionally, randomized grid search will be performed to tune the model. Results are shown as follows:

| Model             | RMSE in Train-set | RMSE in CV | RMSE in Randomized  Grid Search | RMSE in Test-set |
|-------------------|:-----------------:|:----------:|:-------------------------------:|:----------------:|
| XGBoost Regressor |         3         |     16     |               18.9              |         7        |

Model's performance improved significantly because of the outlier's inclusion. Although the RMSE in cross-validation increased by 13 seconds, it's not a big amount of time to conclude that the model suffers overfitting. The RMSE in randomized grid search was 18.9 seconds, showing no improvements with the initial model.

Finally, the model performance in the test-set shows a RMSE of 7 seconds. This result shows that the model has a good performance in unseen data, which allows the model to generalize and predict new data with a small error
