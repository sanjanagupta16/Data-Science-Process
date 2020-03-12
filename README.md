# DATA 598C-Data-Science-Process

This is the project done for the Data 598C course at University of Washington. 

## Problem Statement

Airbnb has had an exponential growth since 2009 with over 650K hosts and over 6 million Airbnb listings worldwide with United States being the top destination for guests. 

![](https://github.com/sanjanagupta16/Data-Science-Process/tree/master/images\airbnb_trend.png)

The number of new hosts have been steadily increasing since 2008, however, in the last couple of years there has been a decrease in this number.

Hosts are expected to set their own prices for their listings. Although there are general guidances provided by Airbnb, there isn't an easy way to determine the best price to rent out a listing. Since the market is competitive, it's important for the hosts to set an optimal nightly price for their properties. Using a calculated average price would not be ideal since the price of a property depends on several listing features.

The purpose of this project to predict the base price for properties in Seattle based on these features. This is done by first analyzing features that can impact the price, and then building a prediction model for the same.

## Data

The data set used for this project comes from Kaggle's Seattle Airbnb Open Data which was scraped on 25th June, 2018.

The data set can be found here - [data](https://www.kaggle.com/airbnb/seattle).

This data contains information about the following:

1. All Seattle listings: close to 4000 entries with 92 features.

2. Calendar data: contains listing ID, price and availability for that day.

3. Reviews data: which contains the listing ID along with  detailed review/comment.

For the purpose of this project, only the listings data set is used to build the prediction model. This was because analyzing the Reviews data would require some natural language processing. The Calendar data would require time series analysis for finding seasonality trends. This was considered out of scope for now, it would be implemented in the future.

## Data Cleaning/Processing

Some of the important features this project will look into are the following:

* Price: nightly price for each listing

* Accommodates: the number of guests  that the listing accommodates

* Room Type: Private room/Shared Room/Entire apartment

* Number of Bedrooms included in the listing

* Number of Bathrooms included in the listing

* Property Type: House/Apartment/Bungalow/Dorm

* Whether or not a host is a superhost

* Type of Cancellation Policy - Strict/Moderate/Flexible

* Availability 365 - Total number of days the listing is available in a year.

* Cleaning Fee

* Extra People

* Neighborhood

* Location of the property: lat-long coordinates, zipcode

* Minimum Nights

* Maximum Nights

* Instant Bookable

* Bed Type 

* Number of Reviews

As a part of data cleaning and processing, the following conversions took place:

* Price variables (with $) converted to numeric to remove $ - price, cleaning_fee, extra_people, security_deposit

* Categorical Variables converted to factor - response_time, host_is_superhost, neighborhood_cleansed, property_type, room_type, bed_type, calendar_updated, cancellation_policy, instant_bookable, bed_type

* Character variables converted to numeric - response_rate, acceptance rate

All rows with NA's and null values were also removed.

Most of the free text variables like - host description, summary, written reviews, scrape ID, neighborhood overview, notes, transit, URL, host_about were dropped since this would require Natural Language Processing.

## Feature Engineering

As a part of feature engineering, `price` and `accommodates` columns were log-transformed.

This was done because: on plotting the `Price` data, it was found to be highly skewed:
![](images\price.png)

On log-transforming this column, it was less skewed which made the results more interpretable:
![](images\log_price.png)

Also, looking at assumptions for a linear model with price as a response, it was found to violate the normality assumption:

![](images\normality_price.png)

The same with a log transformed price column resulted in the residuals being normally distributed.

![](images\normality_log.png)

Similar results were found for `accommodates` column and was hence log-transformed.

The data was also partitioned into training and test data.

For `Property_type`, since a lot of categories contained very few listings, they were changed to "Other" category.

## Model Selection

### Linear Regression

Since this is a prediction problem, with a single dependent variable `price` and  multiple independent variables, a multiple linear regression model was chosen. 

The predictor variables considered are: `accommodates`, `bedrooms`, `bathrooms`, `room_type`, `property_type`, `host_is_superhost`, `cancellation_policy`, `availability_365`, `cleaning_fee`, `extra_people`, `latitude`, `longitude`, `minimum_nights`, `maximum_nights`, `instant_bookable`, `bed_type` and `number_of_reviews.`

A correlation matrix was also built to check if any of the variables used were highly correlated. There was some correlation found, but it was not strong enough to worry multicollinearity.

![](images\corr_plot.png)

1. The initial linear regression model was fitted with the predictor variables mentioned above with price as response. Plotting this model:

![](images\LM1.png)

   - From the residual plot, we can see that the data is linear.
   - From the Normality Q-Q Plot, the residual is not normally distributed.
   - From the Scale-Location plot, there isn't any heteroskedasticity.


2. The second linear regression model was fitted with the predictor variables mentioned above with `log_price` as response. This was a better fit than the previous model. Plotting this model:

![]images\LM2.png)

   - From the residual plot, we can see that the data is linear.
   - From the Normality Q-Q Plot, the residual is more normally distributed than the previous model.
   - From the Scale-Location plot, there isn't any heteroskedasticity.

3. The next linear regression model was fitted with the predictor variables mentioned above using `log_accommodates` instead of accommodates with log price as the response variable. This was found to be the best fit for linear regression.

![](images\LM3.png)

   - From the residual plot, we can see that the data is linear.
   - From the Normality Q-Q Plot, the residual is more normally distributed.
   - From the Scale-Location plot, there isn't any heteroskedasticity.
   - This model gave a higher $R^2$ value than the previous model, hence this was selected as the best linear regression model to predict price.

Predictor variables that were found to be highly significant are: `room_type Shared`, `room_type Private`, `log_accommodates`, `bedrooms`, `bathrooms`, `host_is_superhost`, `cleaning_fee`, `security_deposit`, `instant_bookable`, `longitude`, `latitude`, `minimum_nights`, `number_of_reviews` and `availability_365`.

### Decision Trees / CART

CART model was chosen to predict a continuous variable `price`. This was done to compare the results of a Linear Regression model with that of a Regression Decision Tree.

1. As a baseline, a decision tree was created using bedrooms to predict the log price.
![](images\cart1.png)

2. Final decision tree was created using the same set of predictor variables used in the linear regression model to predict log price. This was found to be the best fit for decision tree.
![](images\CART2.png)

It was found that variables that were significant were : `bathrooms`, `bedrooms`, `cancellation_policy`, `cleaning_fee`, `extra_people`, `latitude`, `log_accommodates`, `longitude`, `maximum_nights`, `minimum_nights`, `number_of_reviews`, `property_type`, `room_type` and `security_deposit`.

### Random Forest

Random Forest model was chosen since typically, it  provides a higher accuracy compared to Decision Trees. It also controls for correlation between predictors, making it a better model than linear regression.

1. The baseline model was fitted using the same set of predictor variables were used to predict log price with mtry = 8. 

![](images\rf1.png)

2. Hyper parameter tuning was performed on `mtry` using Grid Search. `mtry = 17` was selected as the final parameter. Also, the nodesize and ntree (number of trees) parameters were also tuned to find the best fit.

![](images\rf2.jpeg)

It gives us the top 10 variables that are significant:

![](images\rfresult.PNG)

### Generalized Linear Model

The same set of predictor variables were used to predict log price using a Gaussian link function. 
The results are similar to the linear regression model:

![]images\glm.png)

   - From the residual plot, we can see that the data is linear.
   - From the Normality Q-Q Plot, the residual is normally distributed.
   - From the Scale-Location plot, there isn't any heteroskedasticity.
   
It was found that variables that were significant were: `Room_Type_Private`, `Room_Type_Shared`, `Accommodates`, `Bedrooms`, `Bathrooms`, `Property_Type`, `Host_is_Superhost`, `Availability_365`, `Cleaning_fee`, `Security_Deposit`, `Instant_Bookable`, `Minimum_Nights`, `Latitude`, `Longitude`, `Number_of_Reviews`


### ANOVA

Using ANOVA, `property_type` and `neighborhood` were found to be significant.

This was done to check if the categorical values - neighborhood and property type have any significance on price.

More analysis on `neighborhood` will need to be done to see how it fits with all the models.

## Model Training and Assessment

A test and train data was prepared where 75% of the data was split into train, and the remaining 25% of the data was used for test.
This was accomplished using an R package - caTools.

All models were assessed on the same parameter - $R^2$ value.

Actual vs Predicted graphs were also plotted.

## Model Evaluation

1. $R^2$ value was used to evaluate the accuracy of the models.

![](images\r2.PNG)

- For the linear regression model, $R^2$ value was found to be 69.82%.
- Using Decision Trees, the $R^2$ value was found to be 61.69%.
- Using a Random Tree Forest model, the $R^2$ value was found to be 66.94%.
- Using a Generalized Linear Model, the $R^2$ value was found to be 68%.

Linear Regression model has the highest $R^2$ value and therefore, has the highest accuracy.

But, $R^2$ value is not sufficient to assess a model's accuracy.

2. Actual vs Predicted graphs were plotted for all 4 models:

![](images\actual vs predicted.PNG)

3. RMSE was used to evaluate the prediction error of all the models.

![](images\rmse.PNG)

- Linear Regression has an RMSE value of 0.335
- Decision Trees have an RMSE value of 0.335
- Random Forest has an RMSE value of 0.327
- GLM has an RMSE value of 3.264

Based on this, Random Forest model has the least prediction error, and hence is the best model.

4. The models were evaluated based on a utility function created that calculates the profit predicted using each model. This utility function sums up the difference between Actual and Predicted values. 

![](images\util.PNG)

- A profit of $1978 is predicted with Linear Regression.
- A profit of $1249 is predicted with Decision Trees.
- A profit of $2815 predicted with a Random Forest model.
- A profit of $1886 is predicted with GLM model.

Based on this, Random Forest model gives the highest profit.

## Recommendation

1. Currently, the Random Forest model performs better compared to the other models. 
2. There are a few more features that can be looked into to see if they influence price - 

- Amenities
- Reviews and Review Ratings (reviews data) - this would require sentiment analysis.
- Seasonality data (calendar data)

3. Models should also be assessed on other goodness of fit metrics Adjusted $R^2$ and MAE.

4. Hyper-parameter during can be done for Decision Trees to improve accuracy.

5. Some more feature engineering could be done to calculate the proximity to airports, light rail station etc.

6. It might also be worthwhile to look at the outliers for price and capping them at a threshold (99 percentile) to see if the results change for the models.

7. Modeling techniques like XGBoost, Support Vector Regression, Neural Networks can also be applied.

## Code

The markdown file can be found here - [code](https://github.com/sanjanagupta16/Data-Science-Process/blob/master/markdown_Seattle.Rmd)
