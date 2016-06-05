# Fabric Softener Data Analysis
Analyse the customer trends based on Stock keeping Unit of fabric softner products. The SKU is an identification number of a particular product and tells about different attributes of that product. Most Consumer choice models in marketing make use of ‘Brand’ as the fundamental unit of analysis, however many of the decisions made by the consumers, manufacturers, and retailers occur at the level of stock keeping unit. Hence it is important to consider the SKU attributes to better understand consumer trends.

## Overview of Dataset
1.	D1PUR.DAT: This file contains the household purchase history data. It contains household ID for purchase and trip information. 

> The trip info variable is in AAABBBCCC format where:
- AAA = IRI week
- BBB = store#
- CCC = SKU# purchased.

2.	MERCH.DAT: This file contains store environment information. The data is divided in 5 fields i.e. SKU, Store ID, IRIweek, price paid and merchandizing info.

> The format of the merchandising variable is AAABCD where
- AAA = regular price
- B   = ignore
- C   = display
- D   = feature

3.	ARSP.DAT: Contains the average regular selling price of each SKU in each store. It contains SKU, store ID, and ARSP- average regular selling price of each SKU.
4.	BRSINFO.DAT: Contains the attribute information for each SKU.  It contains SKU, SKU description Brand, Size, formula and other coded info for SKU attributes.
5.	IRIdates.xls: This file contains the week of purchase recorded as IRI week. The measure used for IRI where week 1 corresponds to the week ending on 09/09/79. The file contains a mapping with week number and Week ending date

## Data Cleaning & Preparation

1.	The data provided in the .dat files were encoded so we had to clean and prepare the data for our purpose of running the model.
2.	Dummy variables created from the MERCH.dat file, where merchandising variable was in format AAABCD:

> price_cut = AAA - price_paid (if the result is < 0, price_cut = 0)
- Display = 1 if C >= 1; 0 otherwise
- Feature = 1 if D >= 1; 0 otherwise

3-	The names of the Brand, Form, Formula and Size was in the column format with 1s and 0s values in it. To perform the Multinomial Logistic Regression (MLR) on this data, we had to have all the names of the brand in single column. To achieve this task, we used EXCEL quick formula: 
> INDEX (B$1: K$1, MATCH (MAX (B2:K2), B2: K2, 0))
- Wherever 1 is found in the row, it will paste corresponding brand name.
- The same task was performed for SIZE, FORMULA and FORM also

4-	Data was further split into training, calibration and forecast weeks.
>-	Training week from IRIWeek 592 to IRIWeek 641
-	Calibration week from IRIWeek 642 to IRIWeek 643
-	Forecast weeks from IRIWeek 644 to IRIWeek 669

## Visualization

1. Total number of products of each brand with different sizes. As we can see from the below graph that medium size sells the most, whereas XL sells least. Also we can observe that for brand DWN small size products are selling more whereas for brand SNG medium size products sell more.
<br>
<img src="https://github.com/prashant8488/Fabric-Softener-Data-Analysis-/blob/master/images/Brand-Size_Quantity.jpeg">
<br>
2. The below figure shows the total number of SKU per brand. This can be interpreted as the number of items sold by a brand. We can observe that DWN and SNG are the most selling brands, whereas ARM is the least selling brand.	
<br>
<img src="https://github.com/prashant8488/Fabric-Softener-Data-Analysis-/blob/master/images/No.%20of%20SKUs%20per%20Brand.jpeg">
<br>
3. The below line graph explains the number of customer buying fabric softener per week. We can observe that week number 607 and 635 has high number of customer visits. Looking at the IRIweek data we find that these weeks were in the month of April and November respectively.
<br>
<img src="https://github.com/prashant8488/Fabric-Softener-Data-Analysis-/blob/master/images/Nofcust.jpeg">
<br>
4. The below figure shows the formula used by different Fabric softner Brands. The X-axis represents the Formula and Y-axix the the product quantities using that formula. We can observe that maxumum number of brands use the formula RG. From the data It can also be interpreted as more customers prefer products with formula RG.
<br>
<img src="https://github.com/prashant8488/Fabric-Softener-Data-Analysis-/blob/master/images/brand-formula.jpeg">
<br>
5. The below graph shows the weekly sales of the fabric softener. We can observe that week 16 has the maximum sales i.e. $550 approx. Also the average weekly sale hover around $200.
<br>
<img src="https://github.com/prashant8488/Fabric-Softener-Data-Analysis-/blob/master/images/Screen%20Shot%202015-11-18%20at%209.00.52%20PM.png">
<br>

## Data Analysis
#### Brand Analysis
We performed the brand analysis using multinomial logistic regression
##### Most Selling brand – DWN
All intercept coefficients of the brands are negative i.e. log odds of preferring other brand over DWN decreases by exponent of coefficient value.
> mlogit.model1 <- mlogit(Brand ~ 1 , data=mldata, reflevel="DWN")

##### Worst selling brand – ARM
All intercept coefficients of the brands are positive i.e. log odds of preferring other brand over ARM increases by exponent of coefficient value.
> mlogit.model2 <- mlogit(Brand ~ 1 data=mldata, reflevel="ARM")

##### Cheapest Brand – CLF
The price coefficient of all other brand price is positive in reference to CLF. With every one unit increase in variable of price the log odd of selecting other brands increase over CLF. Hence, people prefer other brands over CLF
> mlogit.model4 <- mlogit(Brand ~ 1 | Price, data = mldata, reflevel="CLF")

##### Most Expensive Brand - SNG
The price coefficient of all other brand price is negative in reference to SNG. With every one unit increase in variable of price the log odd of selecting other brands decreases over SNG. Hence, people start preferring SNG

### Forecasting customer preferences based on the SKU
- To forecast customer preference of brands for the Fabric Softener, we build multinomial Logistic regression models. There were 3 functions available in R to perform this task i.e. Mlogit, VGLM with family=multinomial and Multinorm function. 
- VGLM function provided more detail that other function and so we used it to figure out which independent variables are more significant than the others. This was concluded by interpreting the p-value which is listed beside each variable.

##### Models:
Using Multinorm function, we developed a normal keeping BRAND as the dependent variable and SKU as the independent variable. DWN was used as reference brand.
> test_model1 <- multinom(traindata$Brand2 ~ SKU, data = traindata)

Running the prediction model on the validate data set, could help us see how well the model is making prediction. It was observed that model is making prediction with 100% accuracy.
- To confirming the above model, we ran the prediction model on forecast dataset and could identify which brand customer would have bought. It is to be noted that BRAND column was deleted from the forecast dataset before performing this operation. Following is the image which shows 0.9981 probability for PRL brand to be bought by customer.

##### Results:
1. From VGLM model, we could conclude that SKU is a significant independent variable to predict BRAND and IRIWeek and HHId are insignificant variables
2. Using Multinorm function, we could predict the BRAND bought by customer with 100% accuracy.

### Analysing dependency on SKU attributes
<br>
<img src="https://github.com/prashant8488/Fabric-Softener-Data-Analysis-/blob/master/images/sku%20dependency.jpg">
<br>

- A linear regression model was build keeping SKU as the dependent variable and other variables as independent. Starting with Kitchen Sink model, we gradually started removing variables which weren’t  explaining much about the attributes
- We concluded that SKU can be explaned with the help of variance in FORMULA2, FORM, SIZE and BRAND. 
- We can observe that Multiple R-squared is 99.82% and Adjusted R-squared is also 99.82%. This signifies there is no over-fitting and no interaction occurring between the variables.

### Analyzing dependency of Price on manufacturer variables
<br>
<img src="https://github.com/prashant8488/Fabric-Softener-Data-Analysis-/blob/master/images/Price%20manuf%20depend.jpg">
<br>
- A linear regression mode build keeping PRICE as dependent variable and other variables as independent. We started with Kitchen Sink model gradually removed the one which seem to have caused interaction i.e SKU. We could achieve a model which explains PRICE upto 87.27%. Variables BRAND, FORM, FORMULA2, SIZE, DISPLAY and FEATURE explains the maximum variance in the PRICE variable.
So, we can conclude that Price of the product actually depends on manufacturing variable.

### Analyzing Loyalty of customer towards brand 
- Assumption: In the forecast dataset, a new column named loyalty was created which has 0 and 1 on the basis whether customer has opted for same SKU in the past i.e. test dataset. If it does then the customer is loyal else it is not.
- Data Preparation: To perform this activity, we performed few operations in MS Excel using VLOOKUP formula and then did analysis.  
- Analysis: Below line plot explains the number of loyal customers for each SKU. We can observe that 2 SKU’s have the maximum number of loyal customers. 

> SKU 62 (Brand FNT, Form B, Formula2 RG, Size MD)
  SKU 44 (Brand DWN, Form F, Formula2 RG, Size SM)

<img src="https://github.com/prashant8488/Fabric-Softener-Data-Analysis-/blob/master/images/Screen%20Shot%202015-11-19%20at%206.25.37%20PM.png">
<br>
### Forecast Sales using moving average (1) model




