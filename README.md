# Power Analysis and Modeling

### Project for DSC 80 at UCSD

### Name: Noah Golder

## Introduction and Question Identification

The power outages dataset contains data from the major outages in the United States from January 2000 to July 2016, including features such as weather information and characteristics about the states and people affected by the outages. There are a total of 1534 outages reported in the dataset.

#### Central Question:

What risk factors may an energy company want to look into when predicting the severity of its next major power outage?

This dataset and question are important because when an outage occurs, it is important to understand the extent of the outage, whether in terms of customers affected or total power loss. This analysis and modeling can promote better predictions and estimations for future power outages.

#### Number of Rows:
1534

#### Relevant Columns:

YEAR: Year outage occurred

MONTH: Month outage occurred

U.S._STATE: State where outage occurred

CLIMATE.REGION: U.S. Climate region determined by National Centers for Environmental Information, with 9 total regions

CLIMATE.CATEGORY: Cold, warm, or normal climate

OUTAGE.START.DATE: Start date of outage

OUTAGE.START.TIME: Time of day of outage start

OUTAGE.RESTORATION.DATE: End date of outage

OUTAGE.RESTORATION.TIME: Time of day of outage end

CAUSE.CATEGORY: Cause of outage as identified by authorities

OUTAGE.DURATION: Total time duration of outage

DEMAND.LOSS.MW: Estimated total electrical power demand lost during the outage

CUSTOMERS.AFFECTED: Total customers affected by outage

## Data Cleaning and Exploratory Data Analysis

#### Data Cleaning:

1. First, I removed initial rows in the dataset that were just descriptions of the dataset. I then used the 'variables' row as the columns for the dataset. I also dropped the unnecessary 'OBS' column.

2. I kept only the necessary columns, as specified in the relevant columns section.

3. I combined OUTAGE.START.TIME with OUTAGE.START.DATE to create a new column called OUTAGE.START, which I converted to pd.Timestamp. I also combined OUTAGE.RESTORATION.TIME with OUTAGE.RESTORATION.DATE to create a new column called OUTAGE.RESTORATION, which I converted to pd.Timestamp. This change will make it easier to analyze time-related data.

4. I replaced the year and month columns with the respective year and month attributes from OUTAGE.START.DATE to ensure data consistency. I also added a 'DAY' column that contains the day of the week of the outage. This change will allow my model to incorporate various time-related features.

5. I converted all numerical values to floats so that they could be incorporated in analysis and modeling.

Note: I did not change 0 to np.nan values. These mostly occurred in the DEMAND.LOSS.MW column, which means that there is no outage demand loss. This value can occur for smaller outages that don't affect widely inhabited areas, which contrasts with the actual missing values in outage demand loss, so I decided to keep the 0 values.


Here is a sample of the cleaned dataframe, with several selected columns.

| U.S._STATE   | CAUSE.CATEGORY     | CLIMATE.REGION     |   CUSTOMERS.AFFECTED |   OUTAGE.DURATION |
|:-------------|:-------------------|:-------------------|---------------------:|------------------:|
| Minnesota    | severe weather     | East North Central |                70000 |              3060 |
| Minnesota    | intentional attack | East North Central |                  nan |                 1 |
| Minnesota    | severe weather     | East North Central |                70000 |              3000 |
| Minnesota    | severe weather     | East North Central |                68200 |              2550 |
| Minnesota    | severe weather     | East North Central |               250000 |              1740 |


#### Univariate Analysis:

Pictured below is a histogram of the number of customers affected by each outage. The data appears slightly right-skewed, with most of the data concentrated below about 200,000 people per outage. On the right side of the distribution, there are several extreme outages where millions of customers were affected.

<iframe
  src="assets/Hist_Customers_Affected.html"
  width="800"
  height="600"
  frameborder="0"
  display="block"
></iframe>


#### Bivariate Analysis:

Pictured below is a box plot of the number of customers affected by the outage based on the cause category of the outage. For severe weather, system operability disruption, and equipment failure, there seem to be larger values for the number of customers affected overall. There are also many outliers in the number of customers affected.

<iframe
  src="assets/Box_Plot.html"
  width="800"
  height="600"
  frameborder="0"
  display="block"
></iframe>


#### Interesting Aggregates:

Pictured below is a table showing the average outage duration based on climate category (columns of the table) and cause category (rows). Overall, it appears that fuel supply emergencies, public appeals, and severe weather outages have a greater average duration. It also appears that average outage durations are fairly evenly distributed among cold, normal, and warm climates based on the 'Total Average' row, though they are slightly lower for normal climates.


|     CLIMATE.CATEGORY          |     cold |   normal |     warm |
|:------------------------------|---------:|---------:|---------:|
| equipment failure             |   327.5  |  3201.43 |   505    |
| fuel supply emergency         | 17433    |  7658.82 | 22799.7  |
| intentional attack            |   709.54 |   505.44 |   317.77 |
| islanding                     |   259.27 |   142.18 |   209.83 |
| public appeal                 |  2125.91 |  1376.53 |   596.23 |
| severe weather                |  3293.79 |  4082.53 |  4416.69 |
| system operability disruption |   637.26 |   941.02 |   494.69 |
| Total Average                 |  2901.35 |  2666.11 |  2837.37 |



## Assessment of Missingness

#### NMAR Analysis:

CUSTOMERS.AFFECTED has .29 of its values missing. CUSTOMERS.AFFECTED could be NMAR data, since if there are very few customers affected, then the data compilers may not have reported this feature. Outages with higher customers affected would most likely be reported, however, because they are generally more important for constituents.

I would like to obtain the collection method used for each outage, as each method could affect whether the data was reported. Certain authorities might be okay with underreporting the number of customers affected for smaller outages, for instance.

#### Missingness Dependency:

I tested the missigness dependency of OUTAGE.DURATION.MW using the columns of CAUSE.CATEGORY and CLIMATE.CATEGORY.

##### Cause Category

Null Hypothesis: The distribution of Cause Category is the same when Demand Loss is missing vs not missing.

Alternate Hypothesis: The distribution of Cause Category is different when Demand Loss is missing vs not missing.

Test Stastic: Total Varation Distance

Pictured below is the missigness for the Demand Loss based on the various Cause Categories.

<iframe
  src="assets/Missingness.html"
  width="800"
  height="600"
  frameborder="0"
  display="block"
></iframe>

Significance Level: .05

P-Value: 0.0

Conclusion: Reject the null, indicating that the missigness of Demand Loss is dependent on Cause Category.

Pictured below is the null distribution for the permutation test, with the observed statistic on the right.

<iframe
  src="assets/Missingness_Dist.html"
  width="800"
  height="600"
  frameborder="0"
  display="block"
></iframe>



##### Climate Category

Null Hypothesis: The distribution of Climate Category is the same when Demand Loss is missing vs not missing.

Alternate Hypothesis: The distribution of Climate Category is different when Demand Loss is missing vs not missing.

Test Stastic: Total Varation Distance

Significance Level: .05

P-Value: 0.315

Conclusion: Fail to reject the null, indicating that the missigness of Demand Loss is not dependent on Climate Category.


<br><br><br><br><br><br><br><br>












## Hypothesis Testing:

Null Hypothesis: In the population, outage durations in cold and non-cold climates have the same distribution, and the observed differences in our sample are due to random chance.

Alternative: In the population, outage durations in cold climates are greater than those in non-cold climates, on average. The observed difference in our samples cannot be explained by random chance alone.

I am trying to examine the factors that can contribute to the severity of outages, and overall, it makes sense that outage durations for colder climates will take longer because electrical equipment might be damaged due to severe cold weather.

Note: Cold observations include only Climate Categories of cold, while non-cold observations inlcude Climate Categories of normal and warm.

Test statistic: Difference in Means

Significance level: .05 (Standard Significance Level)

Difference in means makes sense because I am looking for a directional test that can determine whether outage durations in cold climates are greater than outage durations in non-cold climates.

P-Value: 0.422

Conclusion: Fail to reject the null, indicating that observed differences in outage duration in cold versus non-cold climates are likely due to random chance. Note: this is not an absolute conclusion -- only a likelihood based on our test.


## Framing a Prediction Problem

Prediction Problem: Predicting Outage Duration for a given outage.

Type: Regression

Response Variable: OUTAGE.DURATION

I chose this response variable because I wanted to help electrical providers predict the severity of an outage in order to provide better estimates for their customers. This information could help customers better plan for a given outage.

Metric to Evaluate Model: I chose r-squared to evaluate my regression model because it gives a tangible estimate of how well the variance in the target variable is explained by the model. RMSE also works, but it is a bit tougher to judge without more context.

At the time of prediction, we would know CLIMATE.CATEGORY, MONTH, YEAR, DAY, CLIMATE.REGION, and U.S._STATE. I am also assuming that we would know CUSTOMERS.AFFECTED, since many electrical companies have monitoring tools and geospatial maps that can detect where certain outages occur and their total customers affected based on this information. If the provider does not have access to this information, however, then my model would not be applicable. Unfortunately, CAUSE.CATEGORY would most likely not be known at the time of prediction because this data is often determined after-the-fact.


## Baseline Model

My baseline model is a multiple linear regression model. I have one quantitative feature, CUSTOMERS.AFFECTED, that I have passed through the model. I have two categorical features, U.S._STATE and CLIMATE.CATEGORY, which I have transformed using One-Hot-Encoding. Both of these categorical features are nominal, as there is no inherent ordering for them.

I chose these features because it makes sense that there should be a relationship between Customers Affected and the overall duration of the outage. The type of climate and state could also affect the overall duration.

I split the training and testing data with a test_size of 0.2, then fit the model.

Overall, I do not believe my model is good because it has an r-squared of .058 on the testing set. In other words, the model captures about 5.8% of the variance in OUTAGE.DURATION, which is not ideal. We would like to make better predictions overall.


## Final Model

For my final model, I decided on a Random Forest Regressor, which averages the predictions of all of its decision trees.

I included four additional features:

CLIMATE.REGION (categorical and nominal), which I transformed using One-Hot-Encoding, to account for additional complexities in the weather that are not accounted for in CLIMATE.CATEGORY.

MONTH and DAY (categorical and nominal), which I transformed using One-Hot-Encoding for both, to account for importances in the time of the week and year. For instance, it is possible that outages are likely to last longer for weekend outages, since less employees are on staff.

YEAR (numerical), which I did not transform, because Standard Scaling doesn't make sense for ordinal values. I included YEAR to account for any relationship between certain events throughout history that might have affected the resources available to electrical companies, such as COVID-19.

Lastly, I applied the Standard Scaler transformation to CUSTOMERS.AFFECTED to reduce the impact of extreme values, especially in the millions.

For the Random Forest Regressor, I used Grid-Search to find the optimal hyperparameters. I chose to optimize the number of trees in the forest to ensure that I have enough trees to make my model robust against outliers, since the regressor uses the average of the tree predictions. In addition, I chose to optimize the max depth for trees, to balance between under and over fitting. Lastly, I chose maximum features per split, again, to balance between bias and variance: I don't want too many features to promote overfitting, but I want enough to have a low bias.

Best hyperparameters:

Regressor_n_estimators (number of trees): 50

Regressor_max_depth: 20

Regressor_max_features: sqrt (sqare-root of the total feature count)

Overall, my model achieved an r-squared of 0.21, meaning that my model accounts for about 21% of the variance in OUTAGE.DURATION. This result is an improvement over the r-squared of .058 from my baseline model.


## Fairness Analysis

My two groups were summer and winter months. I defined summer as being between May and October, and winter as being between November and April.

I wanted to judge the effectiveness of the model on these two groups to determine whether the time of the year might affect how well my model makes predictions on outage duration, which could aid electrical providers in judging when the model is stronger versus weaker. 

Null Hypothesis: Our model is fair. Its rmse for summer and winter months are roughly the same, and any differences are due to random chance.

Alternative Hypothesis: Our model is unfair. Its rmse for colder months is different than its rmse for warmer months.

Test statistic: Absolute Difference in Root Mean Squared Error

Significance level: .05

P-value: 0.422

Conclusion: Fail to reject the null, indicating that the model is most likely fair for summer versus winter months.




