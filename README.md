# Fabric Softener Data Analysis
Analyse the customer trends based on Stock keeping Unit of fabric softner products. The SKU is an identification number of a particular product and tells about different attributes of that product. Most Consumer choice models in marketing make use of ‘Brand’ as the fundamental unit of analysis, however many of the decisions made by the consumers, manufacturers, and retailers occur at the level of stock keeping unit. Hence it is important to consider the SKU attributes to better understand consumer trends.

## Overview of Dataset
1.	D1PUR.DAT: This file contains the household purchase history data. It contains household ID for purchase and trip information. The trip info variable is in AAABBBCCC format where

> AAA = IRI week
> BBB = store#
> CCC = SKU# purchased.

2.	MERCH.DAT: This file contains store environment information. The data is divided in 5 fields i.e. SKU, Store ID, IRIweek, price paid and merchandizing info.

> The format of the merchandising variable is AAABCD where
> AAA = regular price
> B   = ignore
> C   = display
> D   = feature

3.	ARSP.DAT: Contains the average regular selling price of each SKU in each store. It contains SKU, store ID, and ARSP- average regular selling price of each SKU.
4.	BRSINFO.DAT: Contains the attribute information for each SKU.  It contains SKU, SKU description Brand, Size, formula and other coded info for SKU attributes.
5.	IRIdates.xls: This file contains the week of purchase recorded as IRI week. The measure used for IRI where week 1 corresponds to the week ending on 09/09/79. The file contains a mapping with week number and Week ending date

## Data Cleaning & Preparation

1.	The data provided in the .dat files were encoded so we had to clean and prepare the data for our purpose of running the model.
2.	Dummy variables created from the MERCH.dat file, where merchandising variable was in format AAABCD:

> price_cut = AAA - price_paid (if the result is < 0, price_cut = 0)
> Display = 1 if C >= 1; 0 otherwise
> Feature = 1 if D >= 1; 0 otherwise

3-	The names of the Brand, Form, Formula and Size was in the column format with 1s and 0s values in it. To perform the Multinomial Logistic Regression (MLR) on this data, we had to have all the names of the brand in single column. To achieve this task, we used EXCEL quick formula: 
> INDEX (B$1: K$1, MATCH (MAX (B2:K2), B2: K2, 0))
> Wherever 1 is found in the row, it will paste corresponding brand name.
> The same task was performed for SIZE, FORMULA and FORM also

4-	Data was further split into training, calibration and forecast weeks.
>	Training week from IRIWeek 592 to IRIWeek 641
>	Calibration week from IRIWeek 642 to IRIWeek 643
>	Forecast weeks from IRIWeek 644 to IRIWeek 669


