# Social Media's Influence on Cryptocurrencies

## Table of Contents

- [Project Overview](#project-overview)
- [Data Sources](#data=sources)
- [Tools](#tools)
- Data Cleaning
- [Exploratory Data Analysis]
- [Findings](#findings) 
- [Recommendations](#recommendations)
- [Limitations](#limitations)

### Project Overview 
The purpose of this project is to evaluate the impact of social media, specifically Elon Musk's tweets, on cryptocurrency prices with a focus on Dogecoin. Using historical price data and tweet logs, this analysis aims to quantify the influence of social media and explore implications for market volatility.

### Data Sources

The data provided was sourced via Kaggle

### Tools

- Python: Data cleaning, visualization, and analysis.
- Jupyter Notebook: Code execution and exploration.
- Monte Carlo Simulation: Prediction and modeling.

### Data Cleaning

Imported and Cleaned DogeCoin and Tweets Files

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from datetime import datetime

dogecoin_prices = pd.read_csv('DOGE-USD.csv')
dogecoin_prices['date'] = pd.to_datetime(dogecoin_prices['Date'])
elon_tweets = pd.read_csv('TweetsElonMusk.csv')
elon_tweets['date'] = pd.to_datetime(elon_tweets['date'])
dogecoin_tweets = elon_tweets[elon_tweets['tweet'].str.contains('Dogecoin')]

# Filter tweets that mention Dogecoin
dogecoin_tweets = elon_tweets[elon_tweets['tweet'].str.contains('DOGE')]
# Define the time window to analyze price changes after a tweet (in hours)
time_window = 6
# Create a new column to store the price change after each tweet
dogecoin_tweets['Price_Change'] = np.nan
# Iterate through each tweet
for index, tweet in dogecoin_tweets.iterrows():
tweet_date = tweet['date']
tweet_close = dogecoin_prices.loc[dogecoin_prices['date'] == tweet_date, 'C
future_closes = dogecoin_prices.loc[(dogecoin_prices['date'] > tweet_date)
(dogecoin_prices['date'] <= tweet_date
'Close']
if not future_closes.empty:
price_change = (future_closes.iloc[-1] - tweet_close) / tweet_close * 1
dogecoin_tweets.loc[index, 'Price_Change'] = price_change
# Plot the price change after each tweet
plt.figure(figsize=(12, 6))
plt.plot(dogecoin_tweets['date'], dogecoin_tweets['Price_Change'], 'bo-', label
plt.axhline(0, color='r', linestyle='--', label='No Change')
plt.xlabel('date')
plt.ylabel('Price Change (%)')
plt.title("Impact of Elon Musk's Tweets on Dogecoin Prices")
plt.legend()
plt.show()


```


### Exploratory Analysis 

#### Key Metrics and Methods
1. Correlation Analysis:
Examined relationships between tweet frequency and Dogecoin price volatility.
2. Monte Carlo Simulation:
Used historical data to project price trends absent of tweet influence.
3. Visualization:
Highlighted spikes in price corresponding to key tweets.

### Data Analysis 

Ran and PLotted Monte Carlo Simulation

```python
# Filter the data before April 2nd, 2019
start_date = pd.to_datetime('2019-04-02')
filtered_prices = dogecoin_prices[dogecoin_prices['Date'] < start_date]
# Select the 'Close' prices
prices = filtered_prices['Close'].values
# Calculate the daily returns
returns = np.diff(prices) / prices[:-1]
# Calculate the mean and standard deviation of daily returns
mean_return = np.mean(returns)
std_return = np.std(returns)
# Set the simulation parameters
num_simulations = 5000
num_days = 1251
# Perform the Monte Carlo simulation
simulations = np.zeros((num_days, num_simulations))
simulations[0, :] = prices[-1]
for i in range(1, num_days):
drift = 1 + mean_return
shock = std_return * np.random.normal()
simulations[i, :] = simulations[i-1, :] * (drift + shock)


```


#### Monte Carlo Simulation

```python
# Load the Dogecoin price data
#dogecoin_prices = pd.read_csv('dogecoin_prices.csv')
# Filter the data before April 2nd, 2019
start_date = pd.to_datetime('2019-04-02')
filtered_prices = dogecoin_prices[dogecoin_prices['Date'] < start_date]
# Select the 'Close' prices
prices = filtered_prices['Close'].values
# Calculate the daily returns
returns = np.diff(prices) / prices[:-1]
# Calculate the mean and standard deviation of daily returns
mean_return = np.mean(returns)
std_return = np.std(returns)
# Set the simulation parameters
num_simulations = 5000
num_days = 1251
# Perform the Monte Carlo simulation
simulations = np.zeros((num_days, num_simulations))
simulations[0, :] = prices[-1]
for i in range(1, num_days):
drift = 1 + mean_return
shock = std_return * np.random.normal()
simulations[i, :] = simulations[i-1, :] * (drift + shock)

# Plot the Monte Carlo simulations
plt.figure(figsize=(12, 6))
plt.plot(simulations)
plt.title("Monte Carlo Simulation for Dogecoin Price (Before April 2nd, 2019)")
plt.xlabel("Days")
plt.ylabel("Price")
plt.show()

```


#### Tweets along a line graph

```python

import matplotlib.pyplot as plt
# Load the Dogecoin price data
#dogecoin_prices = pd.read_csv('dogecoin_prices.csv')
# Convert the date column to datetime format
dogecoin_prices['date'] = pd.to_datetime(dogecoin_prices['Date'])
# Load Elon Musk's tweets data
#elon_tweets = pd.read_csv('elon_tweets.csv')
# Convert the date column to datetime format
elon_tweets['date'] = pd.to_datetime(elon_tweets['date'])
keywords = ['Dogecoin', 'DOGE',]
dogecoin_tweets = elon_tweets[elon_tweets['tweet'].str.contains('|'.join(keywor
# Merge Dogecoin prices with Elon Musk tweets based on date
merged_data = pd.merge(dogecoin_prices, dogecoin_tweets, on='date', how='inner'
# Plot the Dogecoin price with markers for tweet dates
plt.figure(figsize=(12, 6))
plt.plot(dogecoin_prices['date'], dogecoin_prices['Close'], label='Dogecoin Pri
plt.scatter(merged_data['date'], merged_data['Close'], color='red', label='Elon
plt.title("Dogecoin Price with Elon Musk Tweets")
plt.xlabel("Date")
plt.ylabel("Price")
plt.legend()
plt.xticks(rotation=45)
plt.show()

```


### Findings

#### Key Insights:
- Strong correlation between tweets mentioning Dogecoin and significant price fluctuations.
- Monte Carlo simulations suggest actual prices often exceed predicted ranges following high-profile tweets.

### Recommendations

1. Investors should:
    - Remain cautious of over-reliance on social media trends due to high volatility.
    - Diversify portfolios to mitigate risk associated with influencer-driven markets.
2. Platforms should:
    - Increase transparency in disclosing potential market manipulation.

### Limitations
Limitations
- Data Granularity: Analysis excludes non-text tweet content (e.g., images).
- External Factors: Does not isolate global economic events or broader crypto trends.
