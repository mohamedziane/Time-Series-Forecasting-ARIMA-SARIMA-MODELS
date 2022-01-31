# Time-Series-Forecasting-ARIMA-SARIMA-MODELS

<p align="center">
  <img width="600" height="300" src="https://algorithmia.com/blog/wp-content/uploads/2018/02/chart-1905225_1920-1.jpg">
</p>

## 1. Introduction

Let's suppose I am working for the US federal government as a data scientist in the Health and Environment department. I've been tasked with determining whether sales for the oldest and most powerful producers of cigarettes in the country are increasing or declining.

**Cowboy Cigarettes (TM, *est.* 1890)**  is the US's longest-running cigarette manufacturer. Like many cigarette companies, however, they haven't always been that public about their sales and marketing data. The available post-war historical data runs for only 11 years after they resumed production in 1949; stopping in 1960 before resuming again in 1970. My job is to use the 1949-1960 data to predict whether the manufacturer's cigarette sales actually increased, decreased, or stayed the same. I will need to make a probable reconstruction of the sales record of the manufacturer - predicting the future, from the perspective of the past - to contribute to a full report on US public health in relation to major cigarette companies.

The results of my analysis will be used as part of a major report relating public health and local economics, and will be combined with other studies executed by my colleagues to provide important government advice.

## 2. What are Time Series?

Time series data is just any data displaying how a single variable changes over time. It comes as a collection of metrics typically taken at regular intervals. Common examples of time series data include weekly sales data and daily stock prices. You can also easily acquire time series data from [Google Trends](https://trends.google.com/trends/?geo=US), which shows you how popular certain search terms are, measured in number of Google searches. 

## 3. Visualizing predictor variable <code>**y**</code>:


![cig_sales](https://raw.githubusercontent.com/mohamedziane/Time-Series-Forecasting-ARIMA-SARIMA-MODELS/main/images/CIG_SALES.png)

## 4 The Box-Jenkins method

Building time series models can represent a lot of work for the modeler and so we want to maximize our ability to carry out these projects fast, efficiently and rigorously. This is where the Box-Jenkins method comes in. The Box-Jenkins method is a kind of checklist for you to go from raw data to a model ready for production. The three main steps that stand between you and a production-ready model are identification, estimation and model diagnostics:

<p align="center">
  <img width="600" height="300" src="https://www.researchgate.net/profile/Francisco-Martinez-Alvarez/publication/284516167/figure/fig1/AS:302515112431616@1449136631471/The-Box-Jenkins-methodology.png">
</p>

**4.1 Identification:**
  * Is a Time Series stationary? What differencing will make it stationary?What transforms will make it stationary? What values of <code>**p**</code> and <code>**q**</code> are most promising?

<p align="center">
  <img width="600" height="300" src="https://raw.githubusercontent.com/mohamedziane/Time-Series-Forecasting-ARIMA-SARIMA-MODELS/main/images/CIG_SALES_LOG.png">
  <img width="600" height="300" src="https://raw.githubusercontent.com/mohamedziane/Time-Series-Forecasting-ARIMA-SARIMA-MODELS/main/images/CIG_SALES_LOG_DIFF.png">
</p>


  
    In this plot, the two red dotted lines on either sides of 0 are representing the confidence intervals. These can be used to determine the ‘p’ and ‘q’ values as:
      * q – The lag value where the ACF chart crosses the upper confidence interval for the first time. If you notice closely, in this case q=1.
      * p – The lag value where the PACF chart crosses the upper confidence interval for the first time. If you notice closely, in this case p=1 or 2.
      * S - in SARIMA is 12 as shown from ACF plot

  ![parameters](INSERT AUTOCORR)
 
   
**4.2 Estimation:**
 * We will use a “grid search” to iteratively explore different combinations of parameters. For each combination of parameters, we fit a new seasonal ARIMA model with the SARIMAX() function from the statsmodels module and assess its overall quality. Once we have explored the entire landscape of parameters, our optimal set of parameters will be the one that yields the best performance for our criteria of interest.

 * The output of our code suggests that SARIMAX(1,0,0)x(2, 0, 0, 12) yields the lowest AIC value of 8.0 (This confirmed what we predicted from the ACF and PACF). We should therefore consider this to be optimal option out of all the models we have considered. (This confirmed what we predicted from the ACF and PACF). We should therefore consider this to be optimal option out of all the models we have considered.

 ![grid_search](INSERT PARAM_SEASONAL_AIC_BIC)
 
**4.3 Model Diagnostics:** 

 * Evaluate the quality of the fitting Model by using the test statistics <code>**.summary() method**</code>:
   * Some of these plots also have accompanying test statistics in results.summary() tables. Prob(Q) is the p-value associated with the null hypothesis that the residuals have no correlation structure. Prob(JB) is the p-value associated with the null hypothesis that the residuals are Guaussian normally distributed. If either p-value is less than 0.05 we reject that hypothesis.

     * **Prob(Q):** p-value for null hypothesis that residuals are uncorrelated. Reject the null hypothesis and the residuals are correlated.
     * **Prob(JB):** p-value for null hypothesis that residuals are normally distributed. for our Model Prob(JB) is Zero , which means you should reject the null hypothesis that the residuals are normally distributed (This time the JB value was thrown off by the one outlying point in the time series).

 ![summary](INSERT SUMMARY STATS)


 * diagnostic plots <code>**.plot_diagnostics() method**</code> to make sure the residuals are well behaved:
   * From the normal Q-Q plot, we can see that we almost have a straight line, which suggest no systematic departure from normality. Also, the correlogram on the bottom right suggests that there is no autocorrelation in the residuals, and so they are effectively white noise.
   * **For an ideal model the residuals should be uncorrelated white Gaussian noise centered on zero. The rest of our diagnostics will help us to see if this is true:**
     * **Residuals plot:** looks like our model is working correctly because there's no Obvious structure in the residuals.
     * **Histogram plus estimated density:** The histogram shows us the measured distribution; the orange line shows a smoothed version of this histogram; and the green line, shows a normal distribution. for our model looks fine ... these two lines are almost the same.
     * **Normal Q-Q:** Most of the data points should lie on the straight line (Residuals are normally distributed because most the points lie along the red line, except perhaps some values at either end).
     * **Correlogram (ACF):** 95% of correlations for lag greater than one should not be significant.

 ![plot](INSERT DIAGNOSTICS)

## 5. Application - Forecasting: 

Our model captures the centre of a line that's increasing at a remarkable rate. Cowboy Cigarettes sell more cigarettes in the summer, perhaps due to the good weather, disposable income and time off that people enjoy, and the least in the winter, when people might be spending less and enjoying less free time outdoors.

Remarkably, our ARIMA model made predictions using just one variable. We can only speculate, however, on the causes of the behaviour predicted by our model. We should also take heed that spikes in data, due to sudden unusual circumstances like wars, are not handled well by ARIMA; and the outbreak of the Vietnam War in the 1960s would likely cause our model some distress.

![forecasting](INSERT SARIMA_FORECASTING)

## 6. Conclusion:

| <code>**Metrics**</code>| <code>**Value**</code>|
| :-------| :-----:|
| <code>**Mean Sales (Next 24Months)**</code>| 1,001,452|
| <code>**Lower Sales**</code>| 1,001,145|
| <code>**Upper Sale**</code>| 1,001,760|


**Finaly, let's try to answer the main question:** whether sales for the oldest and most powerful producers of cigarettes in the country are increasing or declining or staying the same:

 * From our SARIMA Model we estimate that Cowboy Cigarettes sales will keep increasing from 1,001,145 to 1,001,760 (Upper)from January 1961 to December 1962.

 * Any increase in tobacco sales is a red flag since Smoking is the leading cause of preventable death. Worldwide, tobacco use causes more than 7 million deaths per year

 * For that reason, On June 22, 2009, President Barack Obama signed into law the Family Smoking Prevention and Tobacco Control Act, giving the U.S. Food and Drug Administration (FDA) comprehensive authority to regulate the manufacturing, marketing, and sale of tobacco products. The new law represents the most sweeping action taken to date to reduce what remains the leading preventable cause of death in the United States.

 * Before enactment of the new law, tobacco products were largely exempt from regulation under the nation’s federal health and safety laws, including the Food, Drug, and Cosmetic Act. The FDA has regulated food, drugs and cosmetics for many decades, but not tobacco products, except in those rare circumstances when manufacturers made explicit health claims.


