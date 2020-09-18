# Volatility_Analysis

This project includes a function running and returning a series of 32 logistic regressions assessing if volatility can predict future upward or downward trends in stock price. The results from the analysis of Apple, the Russel 2000, Nasdaq, Dow Jones Industrial Average, Google, Amazon, IBM, and Goldman Sachs indicate that volatility has highly limited, if any predictive power on stock trends in the context assessed.

<!-- TABLE OF CONTENTS -->
## Table of Contents

* [Introduction](#Introduction)
* [Methods](#Methods)
  * [Requirements](#Requirements)
  * [Code Organization](#code-organization)
  * [Methodology](#Methodology)
  * [A note on P-value correction](#a-note-on-P-value-correction)
  * [Prerequired Functions](#rrerequired-functions)
  * [Main Function](#main-function)
* [Results and Conclusion](#results-and-conclusion)
* [Contact](#contact)
* [Acknowledgements](#acknowledgements)

<!-- Introduction -->
## Introduction
The initial motivation for this project came from discussions with my father about investment strategies. In particular, some of the personal indicators of stock or market weakness are volatility indices, such as the popular S&P volatility index, VIX, made by the Cboe. However, investor help and educational sources such as Investopedia<sub>1 </sub> and Sparrow Wealth<sub>2 </sub> have noted that volatility does not predict upward or downward trends on its own. 

Even so, indices such as the VIX have been called “the fear index”<sub>3 </sub> and if enough traders believe increases in volatility precede short term downward trends in stock price, then it may become a “self-fulfilling prophecy.”  Therefore, the overarching goal of this project is to ascertain if short term changes in implied volatility can predict short term changes in stock price. 

<!-- Methods -->
## Methods

### Requirements

* R 

* R packages:
  * Tidyverse
  * MASS
  * margins
 
Function created and tested with R 4.0.2

### Code organization 

The RMD file contains 3 chunks for set up of the analysis function, and two additional chunks for the data and running of the analysis. 
  * The first chunk contains the librarying of the needed R packages.

  * The second chunk contains all the custom made functions handing data wrangling called upon within the main analysis function.

  * The third chunk contains the “Vol_Analysis” main function, which runs and returns the summaries of 32 logistic regressions.

See comments in RMD file and Function Dictionary for more details.

### Methodology

The daily implied volatility data was from the freely available Cboe volatility indices for Apple, the Russel 2000, Nasdaq, Dow Jones Industrial Average, Google, Amazon, IBM, and Goldman Sachs. These data were chosen because they were both readily available and because of the widespread use of Cboe<sub>4 </sub> volatility indices to measure market volatility. The matching daily stock and daily volume data came from Yahoo Finance.

The bulk of the regression analysis for each stock was conducted with Logistic regressions, coding increases in stock price as Y = 1, and decreases as Y = 0. Further, logistic regressions were chosen over other regression techniques, such as linear regression, since the assumption of logistic regressions are less egregious in this context. For example, logistic regressions do not require linearity, homoscedasticity, or normally distributed errors, all of which are likely violated by these data. 

However, this analysis only controls for daily volume, and logistic regressions still can suffer from omitted variable bias. Therefore, an additional assumption must be imposed that the sum of all systemic biases in omitted factors cancel out, leading to an independent uncorrelated error. Lastly, daily volume was chosen as a control variable due to its potentially confounding effects, and because of its readily available data. For instance, days of high volume could potentially be caused by important company news or other macro-economic trends, which could both have positive or negative impacts on the stock price. 

To investigate if volatility changes can signal future stock price changes, the main question was broken down into several questions, more easily answered with logistic regression hypothesis testing. These main hypothesis questions (HQ.) are:

	Does an increase or decrease in the percentage change in volatility (ddVolatility) after N days, signal an upward or downward percentage change in stock price N days later?
	N = 1,5,10,20 days

∆Stock Price= β_0+β_1*(∂ Volatility)/∂t+β_2*Volume+ ε

	Does a change in volatility (dVolatility) signal a percentage upward or downward change percentage in stock price N days later?
	N = 1,5,10,20 days

∆Stock Price= β_0+β_1*Volatility+β_2*Volume+ ε

	Do groups of percentage volatility changes predict upward or downward percentage changes in stock price? 
	Group 1: < -10% volatility change
	Group 2: between -2.5% to -10% volatility change
	Group 3: between -2.5% and +2.5% volatility change == Reference Group
	Group 4: between +2.5% and +10% volatility change
	Group 5: > +10% volatility change

Reference Group Regression:
∆Stock Price= β_0+β_1*Group3+β_2*Volume+ ε

Main Group Regression:
∆Stock Price= β_0+β_1*Group1+ β_2*Group2+ β_3*Group4 + β_5*Group5+ β_6*Volume+ 

These questions do not encompass all the possible short-term signals volatility or volatility 
changes could possibly entail. However, these questions do encompass a subset of highly useful signals if volatility alone does in fact have predative power.  Further, these HQs are testable with logistic regressions after minor data wrangling to convert stock price into a binary response variable (1 = increase in price, 0 = decrease in price). 

In order to answer the main HQs, 32 regressions were run. Running this many regressions individually for every stock and its volatility would not be feasible for multiple stocks, thus the Volatility_Analysis_Function was constructed in R to simultaneously run and quickly display the regression results for any stock and its volatility measure. See function dictionary and RMD file for further details.


### A note on P-value correction 

The Volatility_Analysis_Function generates 32 regressions with related hypothesis tests drawn from the same data. Using the usual p=0.05 cut off threshold would not be accurate and would likely generate false positives. When predicting stock price movement, the ultimate goal is to make money, and using a false signal can have dire real-world consequences, while disregarding a true signal only limits a stock trader’s “toolbox.” While this can indeed result in negative outcomes, it is unclear and difficult to quantify a priori how negative those consequences may be. 

Therefore, the main function also corrects the regression p-values in the Results data frame using a Bonferroni correction. This correction was chosen since a relatively small number of regressions have been run, and because of its high penalty upon potential false positives. However, because a Bonferroni correction requires independence between hypothesis tests, and since all the main hypothesis questions are not independent, the correction is applied separately within each of the three main HQs. Thus, the corrected p-values are still only closer to the “true” p-values and should be used with some caution.

### Prerequired Functions

To answer the above main H. Qs. questions, a significant amount of data munging was required. A series of custom functions was incrementally created to tailor the raw data to the hypothesis tests at hand. See RMD file for additional comments and details.

1.	Volatility_Change_DF(DF1, DF2, time)
 	* The function passes in the stock and volatility data frames and a “time” argument, then returns a new data frame with the percentage differences between the daily close for both the stock and the volatility on day i to the close on day i + time.
	* Function passes in the stock price data frame, which must include the daily close prices titled at “Close” and the daily volume titled “Volume” in the argument “DF1.”
	* Function passes in the daily volatility data frame which must have daily close volatility titled “Close” in the argument “DF2”. 
	* The “time” argument refers to the number of days to compare. IE: time=3 will compare the difference in both stock and volatility daily closes on day i to day i + 3
b.	* Note the function does not lag the volatility, and the number of rows for DF1 must equal the number of rows in DF2.

2.	Volatility_DF(DF1, DF2, time)
	* The function is identical to the Volatility_Change_DF function above in its inputs. However the data frame output does not have the difference in volatility, but instead the volatility value itself.
	* Note the function does not lag the volatility, and the number of rows for DF1 must equal the number of rows in DF2.

3.	Shift(DF, n)
	* The function passes in a data frame or column of a data frame and lags the data frame or column by n days.
	* Note the first n entries will be lost, and n NA rows will be added to the end to preserve data frame or columns size. This function is only needed inside the function VX_jump.

4.	VX_jump(Stock_DF, VX_DF, days_Stock, days_VX)
	* The function passes in the stock and volatility data frames as well as separate arguments for the number of days to be compared: i days for the comparison stock price closes and j days for the comparison of daily volatility closes. The function returns a new data frame with the % differences for daily stock closes from day i to i + days_Stock, and the % differences for daily volatility from day j to j + days_VX. The function also creates grouping variable to assign percentage volatility changes.
		* The function passes in the stock data frame which must include the daily close prices titled at “Close” and the daily volume titled “Volume” in the argument “Stock_DF”
		* The function passes in the volatility data frame which must include the daily close titled at “Close” in the argument “VX_DF”
		* The “days_Stock” argument sets the number of days to compare for the daily stock price closes. 
		* The “days_VX” argument sets the number of days to compare for the daily volatility closes. 
	* Note this function does lag the volatility, and the stock and volatility data frames must have the same number of rows.

5.	SE_boot(reps, data = PE_DF)
	* This function creates boot strapped standard errors for the custom function “logis_APE”

6.	logis_APE(Reg, DF)
	* This function obtains the average partial effect (APE) for the first 8 regressions in the main function. These regressions use dplyr::lag which the margins package does not currently recognize in R version 4.0.3
	* Returns the APE and the boot strapped standard error.

### Main Function

* Vol_Analysis(StockDF, VXDF)
	
	* The main analysis function passes in the stock data frame and the volatility data frames. Each data frame must have the same number of rows, the stock data frame, StockDF, must have daily closes titled “Close” and daily volume titled “Volume,” while the volatility data frame, VXDF, must only have daily volatility closes titled “Close”
		* See function dictionary and RMD file for additional information
	
	* The function returns a list of lists
		* DeltaVol contains the first 4 regressions answering question 1
		* IncVol contains the next 4 regressions (5-8) answering question 2
		* RefGroup contains the next 12 regressions (9-20) showing if the reference group (-2.5% to 2.5% volatility change) has predictive power on subsequent stock prices
		* Groups contains the last 12 regressions (21-32) showing if any volatility change groups have statistically different predive power over the reference group.
		* Results contains a data frame with the regression estimate for the group, the change in the Odds Ratio (OR), the APE and its standard error, the estimate standard error, and the raw and corrected p-values for the regressions.

<!-- RESULTS AND CONCLUSION -->
### Results and Conclusion

The regression results are mixed for the 8 major stocks and indices chosen in this analysis. Three of the stocks, Goldman Sachs, Apple, and Amazon return all 32 regressions as insignificant at the 5% level after Bonferroni corrections, and Apple and Amazon return all regressions as insignificant even before Bonferroni corrections. 

The other 5 stocks analyzed do not return entirely consistent results between each other. For instance, IBM, Google, and the Russel 2000 all show significance at the 5% level for the dVol_day1 regressions, but the Russel 2000 also shows significance for the dVol_day5 and dVol_day10, while Google and IBM do not. Additionally, the NASDAQ shows no significance for any of the dVol regressions but does show significance at the 5% level for the Groups5.5 and Groups10.5 regressions. Further the DeltaVol_Day1 regression, which was significant for the DOW Jones, has a fairly high standard error for the estimate (0.403) and a fairly low APE (0.0007). Implying that the real-world value, even if statistically significant, may be low. Additionally, the significant regressions for the NASDAQ, Groups5.5 and Groups10.5 do indicate that group 1 (< -10% changes in volatility) have a statistically different predictive power over the insignificant base group (-2.5% to 2.5% changes in volatility), but the Groups5.5 and Groups10.5 respective odds ratios (1.33 and 1.54) and APEs (0.061 and 0.060) do not imply a substantial real world difference.

Of all of the stocks analyzed, only the Russel 2000 dVol_Day1, dVol_Day5, and dVol_Day10 regressions show significance and considerable odds ratios (0.994, 0.988, 0.985 per one unit increase in volatility respectively) and APE’s (-0.034, -0.071, -0.086 per one unit increase in volatility respectively). 

In conclusion, the lack of a clear consistency of which regressions return significant as well as a general lack of real world substantiality to most of the regressions which did return significant, results in a failure to reject the overall null hypothesis that volatility alone, when controlling for daily trade volume, does not predict upwards and downwards trends in stock price. However, in some cases it is possible that changes in volatility may yield some predictive power, but as a whole, it would be unwise to make large bets on volatility predicting stock price changes.



<!-- CONTACT -->
## Contact

Author: Sean Steele

Email: sean.steele.a@gmail.com

<!-- ACKNOWLEDGEMENTS -->
## Acknowledgements

* Thanks to Don Steele for helping with sparking the ideas of the project.

* References 
	1. https://www.investopedia.com/ask/answers/021015/what-best-measure-given-stocks-volatility.asp
	2. https://www.sparrowwealth.com/wp-content/uploads/2016/07/Can-Volatility-Predict-Returns.pdf
	3. https://www.npr.org/2019/01/18/686450979/how-market-volatility-plays-into-the-fear-index
	4. http://www.cboe.com/index/
	







