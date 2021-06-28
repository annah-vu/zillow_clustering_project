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
 - Modules with functions used, complete with docstrings.
 - A README.md with executive summary, contents, data dictionary, conclusion and next steps, and how to recreate this project.
 <br>
 <br>

## 5.) Project Summary
   I used clustering to see if there were any variables that could be grouped together, however, I didn't find anything profound enough to be used in my models. I made a cluster based off of age and square footage. Then I made one of  longitude and latitude. I also made two models with three features, the first of basic house features: bathrooms, bedrooms, dollar per square foot, and age.  The second one was location based to see if I could make some notable neighborhoods by using longitude, latitude, and age. 
According to recursive feature elimination, none of my cluster groups were helpful. I used them just to see if it would work, along with the counties. The test deemed them to not be helpful, and my model performed with more error than the mean as every prediction. 




## 6.) Hypothesis

1.) Log error is affected by square footage, number of bedrooms and bathrooms.

2.) Log error is affected by property age

3.) Log error is affected by location

4.) Log error is affected by price per sq ft.

5.) Log error is affected by combinations of internal property features and combinations of locational features. 


## 7.)  Findings and Next Steps
   - There appears to be distinct groups between longitude/latitude/age, and dollar per sqft/number of bathrooms and bedrooms/age, however, these groups don't help our supervised model. 
   - Using recursive feature elimination, it selected dollar per sqft, number of bathrooms and bedrooms, county, tax amount, tax value, and census block. 

Next steps would be:
 - gather more information on location
 - Look more into census block, try to fix ZIP codes based on latitude/longitude
 - Try out new combinations for clustering with these as well as other columns with other property features.

<br>
<br>

# 8.) The Pipeline: 

## 8a.) Planning :stopwatch:
Goal: Plan out the project
I will be seeing how square footage, bathroom count, bedroom count, longitude, latitude, dollar per square foot, age, and county relate to log error of Zestimates. I will try to cluster by location and by house features to see if it'll be helpful to a supervised regression model. 

First, I will begin by bringing in my data and exploring features to assure that I want to continue with clustering these (and/or others), I can then turn it into a cluster column and use feature selection to see if the clustering helps. 

Hypotheses:  


<br>

## 8b.) Acquire :bulb:
Goal: Have Zillow dataframe ready to prepare in first part of  wrangle.py
In this stage, I used a connection URL to access the CodeUp database. Using a SQL query, I brought in the 2017 Zillow dataset with only properties set for single use, and joined them with other tables via parcelid to get all of their features. I turned it into a pandas dataframe and created a .csv in order to use it for the rest of the pipeline. 
| Acquire Functions    | Purpose                                                        |
|----------------------|----------------------------------------------------------------|
| get_connection()     | Creates a connection link so we can access our data            |
| new_zillow_data()    | Joins our 2017 Zillow data, sets parcelid as index             |
| get_zillow_data()    | Returns Zillow as a data frame, and creates a local Zillow.csv |
<br>


For the next stage: Drop or fill in nulls, remove outliers, rename columns, make new relevant columns.

<br>

## 8c.) Prep :soap:
Goal: Have Zillow dataset that is split into train, validate, test, and ready to be analyzed. Assure data types are appropriate and that missing values/duplicates/outliers are addressed. Put this in our wrangle.py file as well. 
In this stage, I handled missing values by dropping any rows and columns with more than 50% missing data. 

I assured that all columns had a numeric data type, and renamed them for ease of use.

Duplicates were dropped (in parcelid)

Nulls in square footage, lotsize, tax value, and tax amount were imputed with median. (after splitting)

Nulls in calculatedbathnbr, full bath count, region id city, regionidzip, and censustractandblock were imputed with most frequent. (after splitting)

Any remaining nulls after these were dropped. 
I split the data into train, validate, test, X_train, y_train, X_validate, y_validate, X_test, and y_test.
Last, I scaled it on a StandardScaler scaler (I made sure to drop outliers first!) and also returned X_train, X_validate, and X_test scaled. 
<br>
| Prepare Functions                                                   | Purpose                                                                                 |
|---------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| remove_outliers(df, k,col_list)                                     | remove outliers, using IQR * multiplier, from a list of columns in a dataframe          |
| handle_missing_values(df, prop_required_column, prop_prequired_row) | drops columns and rows based on unsatisfied threshold                                   |
| get_counties(df)                                                    | creates dummies from fips column                                                        |
| create_features(df)                                                 | creates new features from original Zillow df (see details in docstring)                 |
| remove_outliers_new_features(df)                                    | removes outliers for newly created features                                             |
| prepare_zillow(df)                                                  | handles outliers, some missing values, adds features, renames columns                   |
| train_validate_test_split(df, target, seed=1349)                    | splits data frame into 56% train, 24% validate, and 20% test sets.                      |
| train_validate_test(df, target)                                     | returns train, validate, test, X_train, y_train, X_validate, y_validate, X_test, y_test |
| impute_nulls(train, validate, test, strategy='mean', col_list=None) | imputes nulls based on strategy to train, validate, and test sets.                      |
| split_X_y(train, validate, test, target)                            | returns X_train, y_train, X_validate, y_validate, X_test, y_test (use after imputing)   |
| standard_scale_data(X_train, X_validate, X_test)                    | scales data using StandardScaler                                                        |
| get_object_cols(df)                                                 | returns names of object columns                                                         |
| get_numeric_C_cols(X_train, object_cols)                            | using object cols, returns names of numeric columns                                     |

<br>

For the next step: run statistical testing and visualize data to find relationships between variables, and create clusters.
<br>


## 8d.) Explore :mag:
Goal: Visualize the data. Explore relationships, and make clusters. Use the visuals and statistics tests to help answer my questions. 
I plotted distributions, made sure nothing was out of the ordinary after cleaning the dataset. 

Plotted a pairplot to see combinations of variables with log error bins.

I ran a few t-tests with the features in respect to log error to test for difference in means. Also did a few correlation tests for continuous variables. 

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
| Baseline                         | 0.1688        | 0.1632        | 0.00  |
| OLS LinearRegression             | 0.1687        | 0.1630        | 0.002 |
| LassoLars                        | 0.1688        | 0.1632        | 0.00  |
| TweedieRegressor                 | 0.1687        | 0.1630        | 0.002 |
| PolynomialRegression (2 degrees) | 0.1687        | 0.1629        | 0.001 |
<br>

Test for OLS Linear Regression:
 - RMSE of 0.177
 - R^2 of 0.003

Mission Failed. We'll get them next time. 
<br>
## 8f.) Delivery
I will be giving a presentation over my findings!
 - All acquire and prepare functions are uploaded for easy replication under wrangle.py.
 - This README 
 - Final notebook that documents a commented walkthrough of my process

## 9.) Conclusion

To conclude... 
My clustering didn't help with my supervised model, however, I could not find the right combinations to make my model beat the baseline for predicting log error either.

 - Log error was different for properties depending on county, number of bathrooms, bedrooms, dollar per square foot, and age.
 - I made clusters with age and square footage, longitude and latitude, and based on property features like age, dollar per sqft, and number of bathrooms and bedrooms. I also made one based on location (neighborhoods) which consisted of longitude, latitude, and age.
 - Appears that Zestimates are pretty good at guessing what the final sale price will be.
 - My best model was my Quadratic (2 degrees), but even though it surpassed the baseline on train and validate, it did not perform better on the test. The RMSE to beat was 0.165, but mine was 0.178. It did better on r^2 at only 0.001 though. 

Next steps: 
<br>

I should see if the other cluster models I made would help, the reason I didn’t use them was because their p-value wasn’t as significant, and they had a lower f score. I should also try clustering other features together, I notice tax amount and tax value seem important too.




## 10.) Data Dictionary 

| Column Name               | Description                              |
|---------------------------|------------------------------------------|
| acres                     | number of acres (lotsize/43560)          |
| age                       | 2017-yearbuilt                           |
| bath_bed_ratio            | bathrooms to bedrooms ratio              |
| baths                     | number of bathrooms                      |
| bathsandbeds              | number of bathrooms and bedrooms         |
| beds                      | number of bedrooms                       |
| county                    | which county property is in              |
| fips                      | FIPS code of property                    |
| land_dollar_per_sqft      | landtaxvaluedollarcnt/sqft               |
| latitude                  | latitude coordinate                      |
| longitude                 | longitude coordinate                     |
| los_angeles               | if property is in Los Angeles =1, else 0 |
| lot_dollar                | dollar value for property land           |
| orange                    | if property is in Orange =1, else 0      |
| rawcensustractandblock    | census bureau data                       |
| regionidzip               | zip code (not accurate)                  |
| sqft                      | square footage of property               |
| structure_dollar_per_sqft | dollar per square foot                   |
| tax_amount                | tax amount of property                   |
| tax_rate                  | tax rate of property                     |
| tax_value                 | tax value of property                    |
| ventura                   | if county is in Ventura =1, else 0       |
| yearbuilt                 | year property was built                  |
| propertylandusetype       | property type                            |
| parcelid                  | property ID                              |




<br>
<br>

## 11.) How to Recreate Project

 - You'll need your own username/pass/host credentials in order to use the get_connection function in my acquire.py to access the Zillow database
 - Have a copy of my wrangle.py, and explore.py files. You can adjust the features to use, how to handle outliers, etc. or just keep it the way I have it! 
 - My final notebook has all of the steps outlined, and it is really easy to adjust parameters.

