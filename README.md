# Multivariate-Timeseries-Modelling
Predict covers in hospitality using SARIMAX and interactive features. Utilizes exogenous variables for accurate forecasts and enhances analysis.

## Observations:
1)	Data Ingestion:
a.	Booking Data: 2285 observations and 8 columns.
i.	Categorical Variables: location, city, city_locations, product.
ii.	Date Variable: date (1 year: 2022).
iii.	Continuous Variable: menu_price, lifecycle_day, covers.
b.	Ad Data: 1021 observations and 20 columns.
i.	Date Variable: date.
ii.	Categorical Variables: city.
iii.	Continuous Variable: 13_17, 18_24, 25_34, 35_44, 45_54, 55_64, 65_ovr, age_unk, audience_network, desktop, device_unk, facebook, instagram, messenger, mobile_app, mobile_web, platform_unk, spend.
c.	External Data: 265 observations and 7 columns.
i.	Categorical Variables: location, urban_city_and_town_sparse_setting.
ii.	Date Variable: week.
iii.	Continuous Variable: cosmopolitans, temp, hpi_averageprice, labour_emp.
2)	Task: “Covers” is the Target Variable that we are trying to forecast. We are trying to analyze if ad spends across different age groups or social media platforms or external data such as temp, house price index etc. has any influence on Covers.
3)	Missing Values: If the missing values are less as in booking data, the categorical missing values are treated with their mode (most repeated category). If it’s a continuous variable then they are treated with median value.
4)	Covers trend over different locations:
a.	Periodicity: Each location shows a periodic pattern suggesting that there might be specific days of the week or times of the month where the number of covers peaks, which could correspond to weekends or specific events.
b.	Volume Variability: The volume of covers varies across locations, with some locations consistently hitting higher peaks (e.g., Location 2) than others. This could indicate differences in popularity, capacity, or perhaps the size of the venue.
c.	Trends: There doesn't seem to be a clear upward or downward trend over the time period shown, suggesting that the data may be for a relatively stable period without long-term growth or decline in covers.
d.	Fluctuations: While the periodicity is clear, there are also irregular fluctuations that could be due to special events, marketing campaigns, seasonal changes, or other external factors affecting patronage.
e.	Comparisons: By looking across the different locations, one could compare business performance and identify which locations might need strategic changes or which ones exemplify best practices.
5)	Seasonal Decomposition of Covers: 
a.	Covers: There are significant periodic fluctuations, suggesting strong seasonal pattern in the data.
b.	Trend: Short term fluctuations (seasonal pattern) are smoothed and we can observe notable peak around beginning and mid of the year. This means there are times during the year where the cover increases.
c.	Seasonal Pattern: The consistent peaks and troughs suggest a repeating cycle, such as weekly or monthly variations, that affect the number of covers.
d.	Residual: After removing the trend and seasonal component, what remains should be random noise and no pattern. But here, we can see some pattern. 
6)	Autocorrelation Function: This plot shows the lag of cover feature with itself at different lags. This helps us to figure out the Moving Average (MA) component. In our plot, we can see at 5% significance there are many lags that are significant from 1 – 7 which indicates the presence of non-seasonal components and we can also clearly see the trend at 7,14,21 etc. which indicates a seasonal trend.
7)	Partial Autocorrelation Function: The partial autocorrelation coefficients, which measure the correlation of the time series with its own lagged values, but after removing the effects of earlier lags. This helps us to figure out the Auto Regressive (AR) component of our model. From our plot we can see that, there are few significant lags. But post lag 21,  the PACF values fall within the confidence interval. This also indicates a seasonal trend. When we have multiple significant value, we start with the lower value to keep the model simple, so that it doesn’t overfit the data.
8)	Dickey–Fuller Test: The P-value is 0.11 and also the test statistic is also greater than all the critical significance value which clearly shows we cannot reject null hypothesis i.e.; time series is not stationary. Given this result, it's likely that differencing is required to make the series stationary. This would imply that 𝐷≥1.
9)	Ljung-Box test: The p-values are low. And hence we reject the null hypothesis that there is no autocorrelation of the residuals. 
10)	Feature Engineering: Created additional interactive features which might add value to covers variable.
a.	total_social_spend: facebook + Instagram + messenger. 
b.	total_mobile_spend: mobile_app_spend + mobile_web_spend.
c.	total_age_category_spend: sum of all the ad spend by age related columns.
d.	total_spend: sum of all the add spend columns.
e.	ad_spends_per_location: location wise ad spends.
f.	revenue_potential: menu price * covers
g.	average_spends_per_cover: total spend / covers
h.	ROAS (Return of ad spend): revenue_potential / total_spend
i.	covers_interaction_over_weekend: covers * is_weekend
j.	covers_interaction_day_of_week: covers * day_of_week
11)	Based on correlation and VIF, many features were removed, if they were not significant and to avoid multicollinearity.
12)	ad spend variable seemed important, hence created a principal components out of them and reduced multicollinearity and dimensionality. 
13)	Ran Regression analysis, in order to understand feature importance for the target variable.
14)	Modelling: 
a.	`Why Sarimax`: I have decided to use a SARIMAX model for this dataset, which includes about 2,285 rows and 23 columns, and exhibits both seasonal and non-seasonal trends in the target variable. I chose not to use LSTM models because the dataset is relatively small, which might hinder the model's ability to learn effectively. Similarly, I dismissed options like random forests, gradient boosting, and XGBoost due to concerns about potential overfitting given the limited data size. Furthermore, these models would require manual creation of features such as lags and rolling averages, increasing the dimensionality and thereby further risking overfitting. In contrast, the SARIMAX model manages both seasonal and non-seasonal components internally, making it a more suitable choice for this analysis.
b.	Results: The training and test RMSE and MAE are close. Suggesting that the model is generalizing well and not overfitting.

i. Average Train RMSE: `3.93`, Average Train MAE: `2.68`.

ii.	Average Test RMSE: `2.30`, Average Test MAE: `1.71`

