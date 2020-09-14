# Volatility_Analysis

This project includes a function running and returning a series of 32 logistic regressions assessing if volatility can predict future upward or downward trends in stock price. The results from the analysis of Apple, the Russel 2000, Nasdaq, Dow Jones Industrial Average, Google, Amazon, IBM, and Goldman Sachs indicate that volatility has highly limited, if any predictive power on stock trends in the context assessed.

<!-- TABLE OF CONTENTS -->
## Table of Contents

* [About the Project](#about-the-project)
  * [Motivations and Purpose](#motivations-and-purpose)
  * [Limitations](#limitations)
* [Getting Started](#getting-started)
  * [Requirements](#requirements)
  * [Code Organization](#code-organization)
  * [Set Up](#set-up)
  * [Usage](#usage)
* [Contact](#contact)
* [Acknowledgements](#acknowledgements)

<!-- Introduction -->
## Introduction
The initial motivation for this project came from discussions with my father about investment strategies. In particular, some of the personal indicators of stock or market weakness are volatility indices, such as the popular S&P volatility index, VIX, made by the Cboe. However, investor help and educational sources such as Investopedia and MarketWatch have noted that volatility does not predict upward or downward trends on its own. 

Even so, indices such as the VIX have been called “the fear index” and if enough traders believe increases in volatility precede short term downward trends in stock price, then it may become a “self-fulfilling prophecy.”  Therefore, the overarching goal of this project is to ascertain if short term changes in implied volatility can predict short term changes in stock price. 

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

The daily implied volatility data was from the freely available Cboe volatility indices for Apple, the Russel 2000, Nasdaq, Dow Jones Industrial Average, Google, Amazon, IBM, and Goldman Sachs. These data were chosen because they were both readily availed and because of the widespread use of Cboe volatility indices to measure market volatility. The matching daily stock and daily volume data came from Yahoo Finance.

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
∆Stock Price= β_0+β_1*Group1+ β_2*Group2+ β_3*Group4 + β_5*Group5+ β_6*Volume+ ε


#### Example 

Input:
```JS
TESTVEC <- c("N", "N", "P")
COVIDTESTProbability(Want = "Positive", Time = 1, Region = "FL", TestVec = TESTVEC)
```
Return:

```JS
[1] The percent chance you are positive is estimated to be
[2] 93.7                                                  
[3] and with 95% confidence is between                    
[4] 51.6                                                  
[5] and                                                   
[6] 98.7  
```
<!-- CONTACT -->
## Contact

Author: Sean Steele

Email: sean.steele.a@gmail.com

<!-- ACKNOWLEDGEMENTS -->
## Acknowledgements

* Thanks to Kelsey Maccuish for helping with testing.

* References to Kurcika et al. https://www.acpjournals.org/doi/10.7326/M20-1495

* References to Andrew N. Cohen, Bruce Kessel https://www.medrxiv.org/content/10.1101/2020.04.26.20080911v1.full.pdf

* References to AP reporting on CDC estimates for COVID19 serological prevalence https://www.nwherald.com/2020/07/21/cdc-antibody-tests-show-virus-rates-10x-higher/afvoinh/





