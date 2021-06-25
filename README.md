# Estimating Home Values from Zillow

### Table of Contents
- 1.) Project Overview
- 2.) Project Description
- 3.) Project Goals
- 4.) Project Deliverables
<br>
<br>

 - 5.) Project Summary
 - 6.) Hypothesis
 - 7.) Findings and Next Steps
<br>
<br>

 - 8a.) Planning
 - 8b.) Data Acquisition
 - 8c.) Data Preparation
 - 8d.) Data Exploration
 - 8e.) Modeling and Evaluation
 - 8f.) Delivery/Deliverables
 <br>
 <br>

 - 9.) Conclusion and Next Steps 
 - 10.) Data Dictionary
 - 11.) How to Recreate


 ## 1.) Project Overview
 Zillow Zestimates are a good way to get an idea of how much a house is worth based on a bunch of features, but a Zestimate is not the final sale price. What drives errors between the zestimates and the final sale price? 
 In this project, I will be working with a Zillow dataset to find drivers for logerror, and create a model that will predict logerror. I'm going to see if using clustering to explore my data will help me find these drivers.
 <br>
 <br>

## 2.) Project Description
   We're going to explore some features and see if they can be made into meaningful clusters within the Zillow data.
  Then, we are to build a regression model to predict a continuous variable (logerror) using features that will help us better predict what drives errors. Since there are heaps of missing data, we'll have to find ways to handle it. 
    
 ## 3.) Goals
  - Deliver a Jupyter notebook going through the steps of the data science pipeline
  - Create cluster models that will help me see what features could affect variances of log error
  - Create a regression model that performs better than the baseline
  - Present to audience about my findings with a walkthrough of my final notebook.
 <br>
 <br>

## 4.) Deliverables
 - Finalized Jupyter notebook complete with comments
 - A README.md with executive summary, contents, data dictionary, conclusion and next steps, and how to recreate this project.
 <br>
 <br>

## 5.) Project Summary
   I used clustering to see if there were any variables that could be grouped together, however, I didn't find anything profound enough to be used in my models. It said my cluster column ranked 36/42 for usage! Although it may have not entirely helped me this time around, it was still nice to see how it could group latitude/longitude/age and other features of a house like bedrooms, bathrooms, and age. 

## 6.) Hypothesis

1.) Log error is affected by square footage, number of bedrooms and bathrooms.

2.) Log error is affected by property age

3.) Log error is affected by location

4.) Log error is affected by price per sq ft.


## 7.)  Findings and Next Steps
   - There appears to be distinct groups between longitude/latitude/age, and dollar per sqft/number of bathrooms and bedrooms/age
   - Using recursive feature elimination used dollar per sqft, number of bathrooms and bedrooms, county, tax amount, and census block

Next steps would be:
 - gather more information on location
 - try to clean up/fill in missing values for other location-based columns such as ZIP code
 - Try out new combinations for clustering

<br>
<br>

# 8.) The Pipeline: 

## 8a.) Planning :stopwatch:
Goal: Plan out the project
I will be seeing how square footage, bathroom count, and bedroom count relate to property value. I believe there will be a 
positive correlation among these variables. 

I also want to look into other features, like age and FIPS code, and see if that will also correlate to property value. 
A lot of these features could play hand in hand and help my model make better predictions.

Hypotheses: Square footage, number of bedrooms, number of bathrooms have a positive relationship with value. Age has a negative relationship with value. FIPS codes have an affect on value, perhaps the means are different across each county. 


<br>

## 8b.) Acquire :bulb:
Goal: Have Zillow dataframe ready to prepare in acquire.py
In this stage, I used a connection URL to access the CodeUp database. Using a SQL query, I brought in the Zillow dataset with only properties set for single use, and were sold in between May-August 2017. I turned it into a pandas dataframe and created a .csv in order to use it for the rest of the pipeline. 
| acquire.py Functions | Purpose                                                        |
|----------------------|----------------------------------------------------------------|
| get_connection()     | Creates a connection link so we can access our data            |
| new_zillow_data()    | Uses a SQL query to return Zillow into a data frame            |
| get_zillow_data()    | Returns Zillow as a data frame, and creates a local Zillow.csv |
<br>


For the next stage: Drop or fill in nulls, remove outliers, rename columns, make new relevant columns.

<br>

## 8c.) Prep :soap:
Goal: Have Zillow dataset that is split into train, validate, test, and ready to be analyzed. Assure data types are appropriate and that missing values/duplicates/outliers are addressed. Put this in a prep.py. 
In this stage, I handled outliers by dropping any rows with values that were 3 standard deviations above or below the mean.
I assured that all columns had a numeric data type, and renamed them for ease of use.
Duplicates were dropped (in parcelid)
Nulls were also dropped, due to the strong correlation between square feet in respect to property value. I did not want to risk making the model dependent on assumed values. 
I split the data into train, validate, test, X_train, y_train, X_validate, y_validate, X_test, and y_test.
Last, I scaled it on a min-max scaler (I made sure to drop outliers first!) and also returned X_train, X_validate, and X_test scaled. 
<br>
| prep.py                                                  | Purpose                                                           |
|----------------------------------------------------------|-------------------------------------------------------------------|
| remove_outlier(df)                                       | removes outliers for certain columns                              |
| clean_zillow(df)                                         | obtain certain columns, makes it ready for use                    |
| train_validate_test(df, target)                          | X sets and y sets                                                 |
| get_object_cols(df)                                      | returns columns with object data types                            |
| get_numeric_X_cols(df, object_cols)                      | returns columns with numeric data types                           |
| min_max_scale(X_train, X_validate, X_test, numeric_cols) | uses MinMax scaler on X sets                                      |
| clean_zillow_taxes(df) *for tax_rates                    | cleans Zillow data for use in calculating tax_rates of properties |
| remove_outlier_tax(df) *for tax rates                    | removes outliers for use in calculating tax_rates of properties   |
<br>

For the next step: run statistical testing and visualize data to find relationships between variables.
<br>


## 8d.) Explore :mag:
Goal: Visualize the data. Explore relationships.  Find answers. Use the visuals and statistics tests to help answer your questions. 
I plotted distributions, made sure nothing was out of the ordinary after cleaning the dataset. 

Plotted a pairplot to see combinations of variables.

I ran a few t-tests with the features in respect to tax_value. Also a few to see if the independent variables were related to each other. 

I found that square footage, bedroom count, and bathroom count were all statistically significant. They are not independent to property value. Bedroom count and bathroom count were also dependent on each other. 
<br>

| explore.py Functions                               | Purpose                                                                  |
|----------------------------------------------------|--------------------------------------------------------------------------|
| plot_variable_pairs(train, cols, hue=None)         | displays pairplot with regression line                                   |
| plot_pairplot(train, cols, hue=None)               | displays pairplot with scatter plots and histograms                      |
| correlation_exploration(train, x_string, y_string) | shows visual correlation between two columns                             |
| get_zillow_heatmap(train)                          | returns a heat map and r values of how each feature relates to tax_value |

<br>
For the next step: Select features to use to build a regression model that predicts property value
<br>

## 8e.) Modeling and Evaluation :chart_with_upwards_trend:
Goal: develop a regression model that performs better than the baseline.

The models worked best with sqft, baths, beds, and age. Polynomial Regression performed the best, so I did a test on it.

| Model                            | RMSE Training | RMSE Validate | R^2   |
|----------------------------------|---------------|---------------|-------|
| Baseline                         | 357,185.61    | 359,454.06    | -3.07 |
| LinearRegression                 | 280,731.20    | 279,672.68    | 0.395 |
| LassoLars                        | 280,731.60    | 279,675.52    | 0.395 |
| TweedieRegressor                 | 280,731.20    | 279,672.68    | 0.395 |
| PolynomialRegression (3 degrees) | 276,310.65    | 274,076.31    | 0.42  |
<br>

Test:
 - RMSE of 272,168.27
 - R^2 of 0.403

Beats the baseline! 
<br>
## 8f.) Delivery
I will be giving a presentation over my findings!
 - All acquire and prepare .py files are uploaded for easy replication.
 - Trello board link
 - Presentation slides
 - This README 
 - Final notebook that documents a commented walkthrough of my process

## 9.) Conclusion

To conclude...
We took a very large Zillow dataset and condensed it down to 37,927 rows to work with. We dropped rows with outliers of 3 standard deviations above or below the mean for that column. 

 -  Square footage was the best feature to determine a property's value. As square footage increased, it seemed that value also went up.

 -  The more bedrooms and bathrooms a house has, the more it was worth. These number of rooms also related to square footage in a positive relationship.

 -  Age was not a huge factor in value, but was helpful in our model's predictions.

 -  Using all of square footage, number of bedrooms, number of bathrooms, and age into a model performed better than the baseline. 

 -  All three counties have similar tax rates, but LA has the highest.




## 10.) Data Dictionary 

| Column Name                  | Renamed   | Info                                            |
|------------------------------|-----------|-------------------------------------------------|
| parcelid                     | N/A       | ID of the property (unique)                     |
| bathroomcnt                  | baths     | number of bathrooms                             |
| bedroomcnt                   | beds      | number of bedrooms                              |
| calculatedfinishedsquarefeet | sqft      | number of square feet                           |
| fips                         | N/A       | FIPS code (for county)                          |
| propertylandusetypeid        | N/A       | Type of property                                |
| yearbuilt                    | N/A       | The year the property was built                 |
| taxvaluedollarcnt            | tax_value | Property's tax value in dollars                 |
| transactiondate              | N/A       | Day the property was purchased                  |
| age                          | N/A       | 2017-yearbuilt (to see the age of the property) |
| taxamount                    | tax_amount| amount of tax on property                       |
| tax_rate                     | N/A       | tax_rate on property                            |


<br>
<br>

## 11.) How to Recreate Project

 - You'll need your own username/pass/host credentials in order to use the get_connection function in my acquire.py to access the Zillow database
 - Have a copy of my acquire, prep, explore .py files. You can adjust the features to use, how to handle outliers, etc. or just keep it the way I have it! 
 - My final notebook has all of the steps outlined, and it is really easy to adjust parameters.
 
 - https://trello.com/b/mVk9grFF/zillow 
 
 - Use Trello Board link to help you get an idea of the steps. 
