# DATA 598C-Data-Science-Process

This is the project done for the Data 598C course at University of Washington. 

## Problem Statement
Aitbnb has had an exponential growth since 2009 with over 650K hosts and over 6 million Airbnb listings worldwide with United States being the top destination for guests. 

Hosts are expected to set their own prices for their listings. Although there are general guidances provided by Airbnb, there isn't an easy way to determine the best price to rent out a listing. Since the market is competititve, it's important for the hosts to set an optimal nightly price for their properties. Using a calculated average price would not be ideal since the price of a property depends on several listing features.

The purpose of this project to predict the base price for properties in Seattle based on these features. This is done by first analysing features that can impact the price, and then building a prediction model for the same.

## Data
The dataset used for this project comes from Kaggle's Seattle Airbnb Open Data which was scraped on 25th June, 2018.

The dataset can be found here - [data](https://www.kaggle.com/airbnb/seattle).
This data contains information about the following:

1. All Seattle listings: close to 4000 entries with 92 features.

2. Calendar data: contains listing ID, price and availability for that day.

3. Reviews data: which contains the listing ID along with  detailed review/comment.

For the purpose of this project, only the listings dataset is used to build the prediction model.

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

* Categorical Variables converted to factor - response_time, host_is_superhost, neighbourhood_cleansed, property_type, room_type, bed_type, calendar_updated, cancellation_policy, instant_bookable, bed_type

* Character variables converted to numeric - response_rate, acceptance rate

All rows with NA's and null values were also removed.

Most of the free text variables like - host description, summary, written reviews, scrape ID, neighborhood overview, notes, transit, url, host_about were dropped since this would require NLP.

## Feature Engineering

As a part of feature engineering, 2 variables were log transformed - Price and Accommodates.

The data was also partitioned into training and test data.

For Property_type, since a lot of categories contained very few listings, they were changed to "Other" category.

## Model Selection

The following models were selected:

1. Linear Regression
Different linear regression models were applied. The response variable is price. 
The predictor variables considered are: 
- Base Linear Regression - 

2. Decision Trees

3. Random Tree Forest

4. Generalized Linear Model

## Model Training and Assessment
A test and train data was prepared where 75% of the data was split into train, and the remaining 25% of the data was used for test.
This was accomplished using an R package - caTools.

There was no hyperparameter during done for Linear Regression, Decision Trees and Generalized Linear Model.

For Random Tree, hyperparameter tuning was performed on "mtry". 
All models were assessed on the same parameter - R^2 value.

## Model Evaluation

## Recommendation

## Future Scope
Look at how amentities influence price.
Apply more models - XGBoost, Neural Network
Look at more goodness of fit metrics - RMSE, MAE
