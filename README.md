# BCIT-HousingProject-DataCleaning-backup

# Business Problem & Hypothesis
When houses are listed for sale on Multiple Listing Services, the seller can ask their agent to list the property at any price they wish. This is often different from what the property ends up selling for, with extreme cases being scenarios where houses are listed way below market in order to create bidding wars, or what is known as “multiple offers.” Agents working with buyers can become increasingly frustrated due to the inability to close on a sale, as their offers are often lost due to higher bids. 

By creating a multivariate linear regression model, we can give a quantifiable answer to how much approximately a house would be bought for, and what a seller could expect to get if they listed their house on the market given those given home facts and features. For our project, we will focus on buyers, and the model will be used to help buyers determine the fair price of a house to avoid overpaying or giving low offers. 

There are notable differences between detached homes and attached homes, including vastly different styles of building (i.e., attached includes duplexes, studio apartments, no lot size, strata fees), we have decided to use data only containing single family homes in the Greater Vancouver area. No leasehold properties or modular homes were used in the data. 


# Data Summary
We retrieved our data from Paragon MLS, and began our data cleaning step right afterwards. For simplicity we only collected monthly data for 2020. We first combined all the datasets from January to December into a single dataset, which contains 4835 rows of transaction records with price and housing information. Then we dropped all the unnecessary text columns, such as: PicCoun, Pics, S/A, Address, Status. Next we dropped the rows with null values in the Sq. feet column and dropped the Depth column as it had inconsistent formatting. We also filtered out rows with erroneous inputs such as built year equalling to 9999. Finally we transformed the List Date column into categorical variables by showing year, month and day separately. We also discovered an imbalance of samples in the TypeDwel column, 98% of the data belongs to type 1, therefore we dropped the 3 other types. 

Details on the Quantitative Models:

Our cleaned data set consists of 11 columns. Since the purpose of our multiple linear regression model is to estimate the price of a house, in the first iteration, we determine the inputs and outputs of the regression model as follows.

Predictor Variables: #House Age #Bedrooms #Bathrooms #Kitchen #Floor Area #Frontage Feet #DaysOnMarket #List Year #List Month #List Day
Response Variable: $Price

We quickly identified some problems with using all the variables to our model.

1.	Non-significance in time series categorical variables
○	We were able to transform list date into dummy variables using One-Hot Encoding in Python, however, given this dataset only includes data in 2020, it is impossible to have any information gain about the house price from listing month or day, unless we have multiple years of monthly data to possibly capture some seasonality of monthly trend, if any. As a result, we dropped #List Year #List Month #List Day.

2.	Days on Market is not a predictor
○	Days on Market refers to the number of days listed on market before it is sold, which is an outcome that may or may not have some relationship with transaction price. However, their relationship is not important for our model because it is to predict the transaction price based on a given housing specifications. It is impossible to know beforehand the days on market until it is sold, therefore, the column #DaysOnMarket is dropped. 

3.	Multicollinearity in some variables
○	The number of bedrooms, bathrooms, kitchens, and total floor area tend to be highly correlated. The graphs below are pair plots between such variables, showing a positive correlation. We realized that we could not use all of them because it would show conflicting coefficients in our summary statistics. We have tested multiple combinations, using 2 to 3 variables at a time, and we decided to drop #Bathrooms #Kitchen.

![image](https://user-images.githubusercontent.com/91990283/158269240-1aa83c8f-a69f-411a-9c2c-34cd0077b904.png)

As a result, the updated inputs and outputs are as follows: 
Predictor Variables: #House Age #Bedrooms #Floor Area #Frontage Feet 
Response Variable: $Price

# Summary Statistics

Multivariate Linear Regression Model:
Price = 612552.3 - 1434.7Age + 986TotFlArea + 4481.9Frontage Feet - 201117Tot BR 

![image](https://user-images.githubusercontent.com/91990283/158269299-3e598fc6-9874-4c76-b993-cc53a7f641a0.png)

Summary Statistical performance/Relevance/Confidence of the Model
Insight 1: Regression statistics and R-square
The R-square of the model is 0.5512, meaning that the proportion of 55% of the variance for the transaction price is explained by the predictor variables of our model. That makes sense because house age, floor area, number of bedrooms are the key determinants of price, but that’s only half of the whole picture. We realized that this could be the best that we can get from those variables because there are other critical variables that we failed to take into account. One obvious example is locational information. Since this data set includes all the transactions from greater Vancouver, and the address column that we have dropped earlier only includes the street name and unit number, we found it difficult to generalize a more specific geographical identification. That means, price variations between Yaletown and Marpole were not being explained by our model. We believe this omission has also caused the MAE to be $659,134 and Standard Error to be $1,084,843, because the same house specs are priced differently in various regions. 

Insight 2: Coefficients
Intercept: The intercept of $612,552 is a constant, which can be interpreted as the price in case that all x variables had value of 0.
Age: A negative coefficient of $1434 can be interpreted as the amount of depreciation as the house gets older by one year. In other words, in general, every year that a house is older from the year it was built results in a decrease in transaction price of $1434. The P-Value of 0.03 means it’s statistically significant at a 95% confidence interval.     
TotFlArea: A positive value of $986 basically means the price per sq.foot. The P-Value is lower than 0.05 (our alpha,significance level 95% ) meaning that it’s statistically significant. This value aligns well with reality that the median price per sq.foot on the east-side of Vancouver as of December 2021 stood at $896, and on the west-side was $1,088. 
Frontage Feet: A positive value of $4481 means the price per sq.foot of frontage area. Frontage is costly as it requires money to pave roads in front of homes, sidewalks need to be poured, drainage needs to be considered etc. The P-value is less than 0.05 (alpha) and standard error is within a thousand dollars, this variable is statistically significant. 
Tot BR: Perhaps unintuitively, the coefficient for the Tot BR was -$201,117.92. Our reasoning was that the more bedrooms you had in a property, the smaller each bedroom had to be to fit within the total floor area. It is possible that people simply prefer fewer but larger rooms over multiple smaller ones. The P-value is less than 0.05 (alpha), meaning this variable is statistically significant. Despite the absolute value of this coefficient being much larger than the others, it is important to note that the average number of bedrooms is around 5, with the maximum in our dataset being 13. This is contrasted with the total floor area of the house, which is measured in square footage and can easily go into the thousands. 

Insight 3: Mean Absolute Error
The MAE of $659,134 is the average absolute difference between predicted value and measured value from our training dataset. We believe that it is worthwhile to compare this number to the average absolute difference between listed price and bought price, which amounts to $166,761. The huge price difference is again tied to our problem definition that buyers often find it difficult to win offers by simply guessing, as they may easily overpay or lose to a higher bid. Our model can give a reference point about the possible range of transaction price, so that realtors and buyers can make proper offers that align with historical trends. 

Full Report: [Formal Report.pdf](https://github.com/Au-Jonathan/BCIT-HousingProject-DataCleaning/files/8248880/Formal.Report.pdf)

