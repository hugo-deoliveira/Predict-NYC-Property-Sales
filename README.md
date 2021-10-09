# Price prediction using Kaggle NYC Property Sales dataset 

## Code and resources used
**Python version**: 3.8  
**Packages**: matplotlib, numpy, pandas, scipy, seaborn, shap, sklearn, statsmodels, xgboost  
**Glossary of terms**: https://www1.nyc.gov/assets/finance/downloads/pdf/07pdf/glossary_rsf071607.pdf

## A first look at the dataset and data cleaning
Querying the data so we can see which information is available in this dataset:
* BOROUGH
* NEIGHBORHOOD
* BUILDING CLASS CATEGORY
* TAX CLASS AT PRESENT
* BLOCK
* LOT
* BUILDING CLASS AT PRESENT
* ADDRESS
* APARTMENT NUMBER
* ZIP CODE
* RESIDENTIAL UNITS
* COMMERCIAL UNITS
* TOTAL UNITS
* LAND SQUARE FEET
* GROSS SQUARE FEET
* YEAR BUILT
* TAX CLASS AT TIME OF SALE
* BUILDING CLASS AT TIME OF SALE
* SALE PRICE  

A very useful information that we could try to extract from this data is **what affect the price**.  
Altough we have a lot of information in this dataset, some data that would be useful is missing, like number of rooms and property conservation level. Also it lacks of categorical classification of neighborhood, which i'll try resolve using One-Hot Encoding.

### The following changes were done:
* Columns like 'EASE-MENT', 'SALE DATE', 'APARTMENT NUMBER' and 'ADDRESS' were deleted due null values or lack of relevance in sale prices change.
* Unfortunately, 'SALE PRICE', 'GROSS SQUARE FEET' and 'LAND SQUARE FEET' have some data missing, but it wouldn't be a smart choice to delete those columns, because price and size are very important variable when you try to predict price, so the model will lack some accuracy.
* Since, LOT, ZIP CODE, BLOCK and BOROUGH were variables to indicate location, I deleted them but, using one hot encoding made variables to indicate the dummy of each Borough of NYC. I kept Borough due its capacity to carry geographical meaning with less variables, as we know the differences between the boroughs. (Eg.: It's reasonble to think the same apartment with everything equal is more expensive in Manhatan than in The Bronx). The following numbers were used to indicate the boroughs: 
```
     Borough 1. Manhattan (New York County)
     Borough 2. The Bronx (Bronx County)
     Borough 3. Brooklyn (Kings County)
     Borough 4. Queens (Queens County)
     Borough 5. Staten Island (Richmond County)
```
Plotting a graph, we can see that Queens and Brooklyn are the boroughs with most sales in this dataset:
![](/output14.png)  

## Model building and results
At first glance, it's good to see the correlation of variables with price, and as the data has been cleaned up, it's good to see which variables correlate with price. As we can see below, after cleaning we see an interesting correlation between Borough 1 (Manhattan) and price, we will work on this idea in the next steps.
```
Correlation of the original      Correlation after cleaning the data
data                           
SALE PRICE           1.000000    SALE PRICE                                              1.000000
GROSS SQUARE FEET    0.449913    BOROUGH_1                                               0.426598
TOTAL UNITS          0.126654    TAX CLASS AT TIME OF SALE_2                             0.345782
RESIDENTIAL UNITS    0.122566    TAX CLASS AT PRESENT_2                                  0.293453
LAND SQUARE FEET     0.060143    BUILDING CLASS CATEGORY_CONDOS - ELEVATOR APARTMENTS    0.291554 
COMMERCIAL UNITS     0.044535    BUILDING CLASS CATEGORY_RENTALS - WALKUP APARTMENTS     0.166577
LOT                  0.012266    TAX CLASS AT TIME OF SALE_4                             0.145026
YEAR BUILT          -0.003779    TAX CLASS AT PRESENT_4                                  0.144650
ZIP CODE            -0.034110    TAX CLASS AT PRESENT_2B                                 0.130020
BLOCK               -0.061357    BOROUGH_3                                               0.125444
```

### Boroughs and Building Class Categories
As we saw above, Queens and Brooklyn are the best selling boroughs, but as we can see in the next five graphs, each neighborhood has different types of building class categories among the top sellers.
![](/output3.png)
#### Top sellers building class categories among Manhatan sales
![](/output4.png)
In Manhattan, Condos - Elevator Apartments are by far the best-selling category. It's easy to see why, Manhattan is the most expensive neighborhood and it's hard to find the cheapest building categories
#### Top sellers building class categories among The Bronx sales
The next two charts show the best-selling building categories for the Bronx and Brooklyn. Both boroughs have family dwellings (two and one family) as the top sellers, although in Brooklyn the Condos have a larger share and it's easy to understand why, as it's a category related to higher income classes and Brooklyn is one boroughs associated with higher income classes than the Bronx.
![](/output5.png)
#### Top sellers building class categories among Brooklyn sales
The last two boroughs charts, Queens and Staten Island, represent the boroughs where the best-selling category is one family dwelling, while in the Bronx and Brooklyn is the two families, which indicates the families have a home in better condition, and it's probably more expensive.
![](/output6.png)
#### Top sellers building class categories among Queens sales
![](/output7.png)
#### Top sellers building class categories among Staten Island sales
![](/output8.png)

### Prediction
Five models were tried to predict price:
* Linear Regression  
RMSE = 0.631, R² = 0.345
* Lasso Regression  
RMSE = 0.634, R² = 0.338
* Ridge Regression  
RMSE = 0.631, R² = 0.345
* Random Forest  
RMSE = 0.577, R² = 0.456
* XGBoost  
RMSE = 0.566, R² = 0.47  

XGBoost has the best results, RMSE and R², so it's a good idea to try to understand the feature importance  of the model variables, it was done in the graph below.
![](/output15.png)  
As we can see, the variable that most makes difference is located in Borough 1, Manhattan. It has a mean impact of nearly $250,000 on price, almost 100,000 more than if it was located in Brooklyn. This means if two apartments have everything equal less the location, an apartment in Manhattan is worth $100k more, this is important in investment decision. However, the R² tell us that this model is able explain only 47% of the total price.
## Conclusion
Further researches with more data are recommended, mainly number of rooms, proximity to services such as subway or supermarkets, some safety index, building conservation and much more. Even so, the current model helps to understand how boroughs influence in price in NYC and can be used in investment decisions.
