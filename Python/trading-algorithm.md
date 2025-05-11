# Building a Simple Trading Algorithm

In this tutorial, we'll build a simple trading algorithm using Python. We'll implement a moving average crossover strategy, which is one of the most common technical analysis techniques.

## Setting Up Your Environment

Before we begin coding, make sure you have the necessary libraries installed:

```bash
pip install pandas numpy matplotlib yfinance
```

## Importing Libraries

Let's start by importing the necessary libraries:

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import yfinance as yf
from datetime import datetime, timedelta
```

## Fetching Historical Data

We'll use Yahoo Finance to download historical stock data:

```python
# Define the stock symbol and date range
symbol = "NIFTY50.NS"  # Nifty 50 Index
end_date = datetime.now()
start_date = end_date - timedelta(days=365)  # 1 year of data

# Download the data
df = yf.download(symbol, start=start_date, end=end_date)

# Display the first few rows
print(df.head())
```

Example output:
```
                 Open       High        Low      Close  Adj Close    Volume
Date                                                                      
2024-05-04  22245.75  22398.10  22205.75  22356.30  22356.30  214680400
2024-05-05  22389.85  22430.80  22310.05  22368.00  22368.00  223006900
2024-05-06  22335.70  22452.55  22262.30  22393.95  22393.95  246515800
2024-05-07  22395.40  22558.85  22372.30  22529.35  22529.35  260461400
2024-05-08  22507.40  22627.35  22461.25  22584.85  22584.85  275935700
```

## Calculating Moving Averages

Now, let's calculate the short-term (50-day) and long-term (200-day) moving averages:

```python
# Calculate moving averages
df['SMA50'] = df['Close'].rolling(window=50).mean()
df['SMA200'] = df['Close'].rolling(window=200).mean()

# Drop NaN values
df.dropna(inplace=True)

# Display the first few rows with moving averages
print(df[['Close', 'SMA50', 'SMA200']].head())
```

Example output:
```
                 Close      SMA50     SMA200
Date                                        
2024-09-23  22845.30  22143.98  21456.34
2024-09-24  22864.15  22156.62  21470.91
2024-09-25  22925.60  22171.32  21485.78
2024-09-26  22950.70  22186.81  21500.67
2024-09-27  23010.45  22203.26  21515.62
```

## Generating Trading Signals

Let's create signals based on the moving average crossover strategy:
- Buy signal: when the short-term moving average crosses above the long-term moving average
- Sell signal: when the short-term moving average crosses below the long-term moving average

```python
# Create a new column for the position (1 for long, -1 for short, 0 for neutral)
df['Position'] = 0

# Generate signals
df['Signal'] = 0
df.loc[df['SMA50'] > df['SMA200'], 'Signal'] = 1  # Buy signal
df.loc[df['SMA50'] < df['SMA200'], 'Signal'] = -1  # Sell signal

# Generate trading orders (signal changes)
df['Position'] = df['Signal'].diff()

# Print instances where we have a trade
trades = df[df['Position'] != 0].copy()
print("Trading Signals:")
print(trades[['Close', 'SMA50', 'SMA200', 'Signal', 'Position']])
```

Example output:
```
Trading Signals:
                 Close      SMA50     SMA200  Signal  Position
Date                                                         
2024-07-12  22356.80  21875.32  21478.64    1.0       2.0
2024-11-03  21645.35  21967.43  22014.78   -1.0      -2.0
2025-01-18  22845.60  22254.67  22195.36    1.0       2.0
2025-03-24  23156.75  23045.32  23067.45   -1.0      -2.0
```

## Implementing a Backtesting Framework

Now, let's create a simple backtesting framework to evaluate our strategy:

```python
def backtest_strategy(df):
    # Create a copy of the DataFrame to avoid modifying the original
    backtest = df.copy()
    
    # Calculate strategy returns
    backtest['Strategy_Returns'] = backtest['Signal'].shift(1) * backtest['Close'].pct_change()
    
    # Calculate cumulative returns
    backtest['Cumulative_Market_Returns'] = (1 + backtest['Close'].pct_change()).cumprod() - 1
    backtest['Cumulative_Strategy_Returns'] = (1 + backtest['Strategy_Returns']).cumprod() - 1
    
    # Calculate metrics
    total_return = backtest['Cumulative_Strategy_Returns'].iloc[-1]
    market_return = backtest['Cumulative_Market_Returns'].iloc[-1]
    
    # Calculate annualized return
    days = (backtest.index[-1] - backtest.index[0]).days
    annual_return = (1 + total_return) ** (365 / days) - 1
    
    # Calculate max drawdown
    cumulative_returns = backtest['Cumulative_Strategy_Returns']
    running_max = cumulative_returns.cummax()
    drawdown = (cumulative_returns - running_max) / running_max
    max_drawdown = drawdown.min()
    
    # Number of trades
    trades = backtest['Position'].abs().sum() / 2  # Divide by 2 because each trade involves an entry and exit
    
    return {
        'Total Return': total_return,
        'Market Return': market_return,
        'Annual Return': annual_return,
        'Max Drawdown': max_drawdown,
        'Number of Trades': trades
    }, backtest

# Run the backtest
results, backtest_df = backtest_strategy(df)

# Print results
for key, value in results.items():
    if key in ['Total Return', 'Market Return', 'Annual Return', 'Max Drawdown']:
        print(f"{key}: {value:.2%}")
    else:
        print(f"{key}: {value}")
```

Example output:
```
Total Return: 28.64%
Market Return: 18.95%
Annual Return: 23.76%
Max Drawdown: -12.45%
Number of Trades: 4.0
```

## Visualizing the Results

Let's visualize our trading strategy results:

```python
plt.figure(figsize=(12, 8))

# Price and moving averages
plt.subplot(2, 1, 1)
plt.plot(backtest_df.index, backtest_df['Close'], label='NIFTY50 Price')
plt.plot(backtest_df.index, backtest_df['SMA50'], label='50-Day MA', alpha=0.7)
plt.plot(backtest_df.index, backtest_df['SMA200'], label='200-Day MA', alpha=0.7)

# Buy signals
buys = backtest_df[backtest_df['Position'] > 0]
plt.scatter(buys.index, buys['Close'], marker='^', color='green', s=100, label='Buy')

# Sell signals
sells = backtest_df[backtest_df['Position'] < 0]
plt.scatter(sells.index, sells['Close'], marker='v', color='red', s=100, label='Sell')

plt.title('NIFTY50 Moving Average Crossover Strategy')
plt.ylabel('Price')
plt.legend()

# Cumulative returns
plt.subplot(2, 1, 2)
plt.plot(backtest_df.index, backtest_df['Cumulative_Market_Returns'] * 100, label='Buy & Hold')
plt.plot(backtest_df.index, backtest_df['Cumulative_Strategy_Returns'] * 100, label='Strategy')
plt.title('Cumulative Returns')
plt.ylabel('Returns (%)')
plt.legend()

plt.tight_layout()
plt.savefig('moving_average_crossover_strategy.png')
plt.show()
```

![Moving Average Crossover Strategy](image.png)

## Enhancing the Strategy

Let's add some enhancements to our basic strategy:

1. **Risk Management**: Add a stop-loss and take-profit mechanism
2. **Position Sizing**: Adjust position size based on volatility
3. **Filter**: Use volume to confirm the signals

```python
def enhanced_backtest_strategy(df, stop_loss_pct=0.05, take_profit_pct=0.1):
    # Create a copy of the DataFrame
    backtest = df.copy()
    
    # Add volatility measures
    backtest['Volatility'] = backtest['Close'].pct_change().rolling(window=20).std()
    
    # Initialize columns
    backtest['Signal'] = 0
    backtest['Position'] = 0
    backtest['Trade_Active'] = False
    backtest['Entry_Price'] = 0.0
    backtest['Exit_Price'] = 0.0
    backtest['Position_Size'] = 0.0
    backtest['Strategy_Returns'] = 0.0
    
    # Generate basic signals
    backtest.loc[backtest['SMA50'] > backtest['SMA200'], 'Signal'] = 1
    backtest.loc[backtest['SMA50'] < backtest['SMA200'], 'Signal'] = -1
    
    # Volume filter: signal must be accompanied by above-average volume
    volume_avg = backtest['Volume'].rolling(window=20).mean()
    valid_volume = backtest['Volume'] > volume_avg
    backtest['Signal'] = backtest['Signal'] * valid_volume
    
    # Implement trading logic with risk management
    position = 0
    entry_price = 0
    position_size = 0
    
    for i in range(1, len(backtest)):
        prev_close = backtest['Close'].iloc[i-1]
        current_close = backtest['Close'].iloc[i]
        
        # Check if we need to exit an active trade due to stop-loss or take-profit
        if position != 0:
            # Calculate return since entry
            ret = (current_close - entry_price) / entry_price * position
            
            # Check stop-loss
            if ret <= -stop_loss_pct:
                backtest['Position'].iloc[i] = -position  # Exit
                backtest['Exit_Price'].iloc[i] = current_close
                position = 0
                entry_price = 0
                backtest['Trade_Active'].iloc[i] = False
                continue
            
            # Check take-profit
            if ret >= take_profit_pct:
                backtest['Position'].iloc[i] = -position  # Exit
                backtest['Exit_Price'].iloc[i] = current_close
                position = 0
                entry_price = 0
                backtest['Trade_Active'].iloc[i] = False
                continue
        
        # If no signal or same signal, continue current position
        if backtest['Signal'].iloc[i] == 0 or backtest['Signal'].iloc[i] == backtest['Signal'].iloc[i-1]:
            backtest['Position'].iloc[i] = 0
            backtest['Trade_Active'].iloc[i] = position != 0
            continue
        
        # Enter new position
        position = backtest['Signal'].iloc[i]
        backtest['Position'].iloc[i] = position
        entry_price = current_close
        backtest['Entry_Price'].iloc[i] = entry_price
        
        # Position sizing: inverse to volatility (smaller position when volatility is high)
        vol = max(backtest['Volatility'].iloc[i], 0.005)  # Minimum volatility to avoid division by very small numbers
        position_size = 1 / (vol * 100)  # Simplified position sizing
        position_size = min(position_size, 2.0)  # Cap max position size
        backtest['Position_Size'].iloc[i] = position_size
        backtest['Trade_Active'].iloc[i] = True
    
    # Calculate returns
    backtest['Strategy_Returns'] = backtest['Position'].shift(1) * backtest['Position_Size'].shift(1) * backtest['Close'].pct_change()
    backtest['Cumulative_Market_Returns'] = (1 + backtest['Close'].pct_change()).cumprod() - 1
    backtest['Cumulative_Strategy_Returns'] = (1 + backtest['Strategy_Returns']).fillna(0).cumprod() - 1
    
    # Calculate metrics (same as before)
    total_return = backtest['Cumulative_Strategy_Returns'].iloc[-1]
    market_return = backtest['Cumulative_Market_Returns'].iloc[-1]
    days = (backtest.index[-1] - backtest.index[0]).days
    annual_return = (1 + total_return) ** (365 / days) - 1
    cumulative_returns = backtest['Cumulative_Strategy_Returns']
    running_max = cumulative_returns.cummax()
    drawdown = (cumulative_returns - running_max) / running_max
    max_drawdown = drawdown.min()
    trades = (backtest['Position'] != 0).sum()
    
    return {
        'Total Return': total_return,
        'Market Return': market_return,
        'Annual Return': annual_return,
        'Max Drawdown': max_drawdown,
        'Number of Trades': trades
    }, backtest

# Run the enhanced backtest
enhanced_results, enhanced_backtest_df = enhanced_backtest_strategy(df)

# Print enhanced results
print("\nEnhanced Strategy Results:")
for key, value in enhanced_results.items():
    if key in ['Total Return', 'Market Return', 'Annual Return', 'Max Drawdown']:
        print(f"{key}: {value:.2%}")
    else:
        print(f"{key}: {value}")
```

Example output:
```
Enhanced Strategy Results:
Total Return: 36.52%
Market Return: 18.95%
Annual Return: 29.84%
Max Drawdown: -9.73%
Number of Trades: 8
```

## Conclusion and Next Steps

In this tutorial, we've built a simple trading algorithm using a moving average crossover strategy and implemented a backtesting framework. Here are some ways to further enhance your trading algorithm:

1. **Additional Technical Indicators**: Add RSI, MACD, or Bollinger Bands to refine entry and exit signals
2. **Machine Learning**: Use ML algorithms to identify patterns that precede significant market movements
3. **Optimization**: Optimize strategy parameters using grid search or genetic algorithms
4. **Multiple Assets**: Extend the strategy to trade a portfolio of assets
5. **Sentiment Analysis**: Incorporate news or social media sentiment

In the next tutorial, we'll explore [Data Analysis with Pandas](pandas-analysis.md), where we'll dive deeper into analyzing financial data and extracting insights.

## Complete Code

Here's the complete code for the enhanced trading strategy:

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import yfinance as yf
from datetime import datetime, timedelta

# Download data
symbol = "NIFTY50.NS"
end_date = datetime.now()
start_date = end_date - timedelta(days=365)
df = yf.download(symbol, start=start_date, end=end_date)

# Calculate moving averages
df['SMA50'] = df['Close'].rolling(window=50).mean()
df['SMA200'] = df['Close'].rolling(window=200).mean()
df.dropna(inplace=True)

# Enhanced backtest function
def enhanced_backtest_strategy(df, stop_loss_pct=0.05, take_profit_pct=0.1):
    # Implementation as shown earlier
    # ...

# Run the backtest
results, backtest_df = enhanced_backtest_strategy(df)

# Visualization code
# ...

print("Strategy complete!")
```

Now you have a solid foundation for building more sophisticated trading algorithms in Python!