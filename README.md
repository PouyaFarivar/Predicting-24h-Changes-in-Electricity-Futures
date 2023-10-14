# Predicting-24h-Changes-in-Electricity-Futures
Predicting 24h changes in Electricity futures for Germany and France. QRT's 2023 data challenge: https://challengedata.ens.fr/participants/challenges/97/

## Overview

### Challenge context
Every day, a multitude of factors impact on the price of electricity. Local weather variations will affect both electricity generation and demand for instance. Long term phenomena, such as global warming, will also have a significant influence. Geopolitical events, such as the war in Ukraine, may affect in parallel the price of commodities, which are key inputs in electricity generation, knowing that each country relies on a particular energy mix (nuclear, solar, hydro, gas, coal, etc). Moreover, each country may import/export electricity with its neighbors through dynamical markets, like in Europe. These various elements make quite complex the modelisation of electricy price in a given country.

### Challenge goals
The aim is to model the electricity price from weather, energy (commodities) and commercial data for two European countries - France and Germany. Let us stress that the problem here is to explain the electricity price with simultaneous variables and thus this is not a prediction problem.

More precisely, the goal of this challenge is to learn a model that outputs from these explanatory variables a good estimation for the daily price variation of electricity futures) contracts, in France and Germany. These contracts allow you to receive (or to deliver) a given amount of electricity at a specified price by the contract delivered at a specified time in the future (at the contract's maturity). Thus, futures contracts are financial instruments that give you some expected value on the future price of electricity under actual market conditions - here, we focus on short-term maturity contracts (24h). Let us stress that electricity future exchange is a dynamic market in Europe.

Regarding the explanatory variables, the participants are provided with daily data for each country which involve weather quantitative measurements (temperature, rain, wind), energetic production (commodity price changes), and electricity use (consumption, exchanges between the two countries, import-export with the rest of Europe).

The score function (metric) used is the Spearman's correlation between the participant's output and the actual daily price changes over the testing data set sample.

### Data description
We provide three csv file data sets: training inputs X_train, training outputs Y_train, and test inputs X_test.

NB: The input data X_train and X_test represent the same explanatory variables but over two different time periods.

The columns ID in X_train et Y_train are identical, and the same holds true for the testing data. 1494 rows are available for the training data sets while 654 observations are used for the test data sets.

#### Input data sets comprise 35 columns:

ID: Unique row identifier, associated with a day (DAY_ID) and a country (COUNTRY),

DAY_ID: Day identifier - dates have been anonymized, but all data corresponding to a specific day is consistent,

COUNTRY: Country identifier - DE = Germany, FR = France,

#### and then contains daily commodity price variations,

GAS_RET: European gas,

COAL_RET: European coal,

CARBON_RET: Carbon emissions futures,

#### weather measures (daily, in the country x),

x_TEMP: Temperature,

x_RAIN: Rainfall,

x_WIND: Wind,

#### energy production measures (daily, in the country x),

x_GAS: Natural gas,

x_COAL: Hard coal,

x_HYDRO: Hydro reservoir,

x_NUCLEAR: Daily nuclear production,

x_SOLAR: Photovoltaic,

x_WINDPOW: Wind power,

x_LIGNITE: Lignite,

#### and electricity use metrics (daily, in the country x),

x_CONSUMPTON: Total electricity consumption,

x_RESIDUAL_LOAD: Electricity consumption after using all renewable energies,

x_NET_IMPORT: Imported electricity from Europe,

x_NET_EXPORT: Exported electricity to Europe,

DE_FR_EXCHANGE: Total daily electricity exchange between Germany and France,

FR_DE_EXCHANGE: Total daily electricity exchange between France and Germany.

#### Output data sets are composed of two columns:

ID: Unique row identifier - corresponding to the input identifiers,

TARGET: Daily price variation for futures of 24H electricity baseload.

## Method

### Analysis Notebook
I have tried to load the data in this notebook and make sense of it. As seen in the graphs the results (y_trian) look random. This means that there is a lot of noise in the final output and the train data
 itself. 

The x_train contains 35 columns of data of which 32 is useful. Since the feature space is big compared to the number of trials in hand, I have tried to find the redundant features. To do this I use the Spearman correlation between each feature and the target to find the redundant features. I have used the Spearman correlation because the final benchmark is done using the Spearman correlation, also because of the nature of the 24-hour change data and its having a lot of noise spearman correlation is a better method than using the Pearson correlation. Thus every feature with below 0.05 Spearman correlation coefficient is removed.

There are some Nan values in each column which are filled with the average of that column.

Also, I have done a 5 standard deviation test to find outlier trails, however, because of the low amount of trials and not many outliers I have not removed the trials with features above 5 std.

Based on the Spearman correlation coefficients between each feature and the target, you can see that the important features for each country are different this means that two models should be made overall, one for each country.

### Final Notebook
In this notebook, I do all the preprocessing chosen in the Analysis notebook and perform them on the train data divided into German and France data. After this is done, the preprocessed data is used to make SVR (Support Vector Regression) models to predict each country’s 24-hour changes in electricity futures.

The hyperparameter of the SVRs and the cutoff at which we remove a feature are tuned to get the best-generalized model. It’s worth mentioning that this is done separately for each country with different models.

The final result has an MSE of below 1 and a Spearman correlation 0f ~0.27 which is ranked in the top 100 in the world.

## Contributors

Pouya Farivar pouyam@gmail.com


