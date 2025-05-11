# Data Structures in Python

Python offers several built-in data structures that are essential for efficient programming, especially when working with financial data and trading algorithms. This guide will cover the main data structures you'll use in your Python projects.

## Lists

Lists are ordered, mutable collections that can store elements of different types.

### Creating Lists

```python
# Empty list
empty_list = []

# List of stock symbols
stocks = ["AAPL", "MSFT", "GOOGL", "AMZN"]

# List with mixed data types
mixed_list = ["NIFTY", 50, 18500.75, True]

# Creating a list of numbers
prices = list(range(100, 110))  # [100, 101, 102, ..., 109]
```

### Accessing List Elements

```python
stocks = ["AAPL", "MSFT", "GOOGL", "AMZN", "NFLX"]

# Indexing (zero-based)
first_stock = stocks[0]      # "AAPL"
last_stock = stocks[-1]      # "NFLX"

# Slicing
tech_giants = stocks[1:3]    # ["MSFT", "GOOGL"]
first_three = stocks[:3]     # ["AAPL", "MSFT", "GOOGL"]
last_three = stocks[-3:]     # ["GOOGL", "AMZN", "NFLX"]
```

### Common List Operations

```python
stocks = ["AAPL", "MSFT", "GOOGL"]

# Add elements
stocks.append("AMZN")         # ["AAPL", "MSFT", "GOOGL", "AMZN"]
stocks.insert(1, "NFLX")      # ["AAPL", "NFLX", "MSFT", "GOOGL", "AMZN"]

# Remove elements
removed = stocks.pop()        # removes and returns "AMZN"
stocks.remove("NFLX")         # removes "NFLX"

# Finding elements
msft_index = stocks.index("MSFT")  # returns 1
has_aapl = "AAPL" in stocks        # returns True

# Length of list
num_stocks = len(stocks)      # returns 2

# Sorting
stocks.sort()                 # in-place sorting
sorted_stocks = sorted(stocks)  # returns new sorted list

# Reversing
stocks.reverse()              # in-place reversal
```

### List Comprehensions

List comprehensions provide a concise way to create lists:

```python
# Creating a list of squares
squares = [x**2 for x in range(1, 6)]  # [1, 4, 9, 16, 25]

# Filtering with conditions
prices = [101.2, 98.7, 102.3, 97.5, 105.8, 95.2, 103.9]
above_100 = [price for price in prices if price > 100]  # [101.2, 102.3, 105.8, 103.9]

# Calculating daily returns
closing_prices = [100, 102, 98, 103, 105, 102]
daily_returns = [(closing_prices[i] - closing_prices[i-1]) / closing_prices[i-1] * 100 
                for i in range(1, len(closing_prices))]
# [2.0, -3.92, 5.1, 1.94, -2.86]
```

## Dictionaries

Dictionaries are unordered, mutable collections of key-value pairs, perfect for storing related data.

### Creating Dictionaries

```python
# Empty dictionary
empty_dict = {}

# Dictionary with stock data
stock = {
    "symbol": "AAPL",
    "company": "Apple Inc.",
    "price": 150.25,
    "volume": 1200000,
    "pe_ratio": 28.5
}

# Nested dictionaries
portfolio = {
    "AAPL": {"shares": 10, "avg_price": 140.50},
    "MSFT": {"shares": 15, "avg_price": 250.75}
}
```

### Accessing Dictionary Data

```python
stock = {
    "symbol": "AAPL",
    "company": "Apple Inc.",
    "price": 150.25
}

# Accessing values
symbol = stock["symbol"]      # "AAPL"

# Using get() (safer, returns None if key doesn't exist)
price = stock.get("price")    # 150.25
sector = stock.get("sector")  # None
sector = stock.get("sector", "Technology")  # "Technology" (default value)

# Keys, values and items
keys = stock.keys()           # dict_keys(['symbol', 'company', 'price'])
values = stock.values()       # dict_values(['AAPL', 'Apple Inc.', 150.25])
items = stock.items()         # dict_items([('symbol', 'AAPL'), ('company', 'Apple Inc.'), ('price', 150.25)])
```

### Modifying Dictionaries

```python
stock = {"symbol": "AAPL", "price": 150.25}

# Add or update entries
stock["sector"] = "Technology"
stock["price"] = 152.75

# Remove entries
del stock["price"]
sector = stock.pop("sector")  # removes and returns "Technology"

# Checking if key exists
has_symbol = "symbol" in stock  # True
```

### Dictionary Comprehensions

```python
# Creating a dictionary from lists
symbols = ["AAPL", "MSFT", "GOOGL"]
prices = [150.25, 290.80, 2750.50]
stock_prices = {symbols[i]: prices[i] for i in range(len(symbols))}
# {'AAPL': 150.25, 'MSFT': 290.8, 'GOOGL': 2750.5}

# Filtering with conditions
stocks = {"AAPL": 150.25, "MSFT": 290.80, "GOOGL": 2750.50, "AMZN": 3380.20}
expensive = {symbol: price for symbol, price in stocks.items() if price > 1000}
# {'GOOGL': 2750.5, 'AMZN': 3380.2}
```

## Tuples

Tuples are ordered, immutable collections, useful for data that shouldn't change.

```python
# Creating tuples
empty_tuple = ()
single_item = (1,)  # Note the comma
stock_info = ("AAPL", "Apple Inc.", 150.25)

# Unpacking tuples
symbol, company, price = stock_info

# Tuples are immutable (can't be changed)
# This will raise an error:
# stock_info[2] = 151.30

# Common tuple operations
length = len(stock_info)  # 3
has_apple = "Apple Inc." in stock_info  # True
```

## Sets

Sets are unordered collections of unique elements, perfect for removing duplicates and membership tests.

```python
# Creating sets
empty_set = set()  # Note: {} creates an empty dictionary, not a set
tech_stocks = {"AAPL", "MSFT", "GOOGL", "AAPL"}  # Duplicates are removed automatically
# Result: {'GOOGL', 'AAPL', 'MSFT'}

# Common set operations
tech_stocks.add("AMZN")
tech_stocks.remove("MSFT")  # Raises error if not found
tech_stocks.discard("NFLX")  # No error if not found

# Set operations
blue_chips = {"AAPL", "MSFT", "JNJ", "PG"}
tech_stocks = {"AAPL", "MSFT", "GOOGL", "AMZN"}

union = blue_chips | tech_stocks  # All stocks from both sets
intersection = blue_chips & tech_stocks  # Stocks in both sets
difference = tech_stocks - blue_chips  # Stocks in tech_stocks but not in blue_chips
symmetric_diff = tech_stocks ^ blue_chips  # Stocks in either set but not both
```

## Practical Example: Portfolio Analysis

Here's an example that combines multiple data structures to analyze a stock portfolio:

```python
# Stock data (symbol -> historical prices)
stock_data = {
    "AAPL": [145.50, 148.75, 146.80, 150.25, 152.00],
    "MSFT": [250.30, 252.40, 249.90, 255.15, 253.80],
    "GOOGL": [2680.70, 2730.50, 2710.25, 2805.10, 2780.90]
}

# Portfolio holdings (symbol -> shares)
portfolio = {
    "AAPL": 10,
    "MSFT": 5,
    "GOOGL": 2
}

# Calculate portfolio value over time
portfolio_values = []

for day in range(len(next(iter(stock_data.values())))):  # Number of days
    daily_value = sum(stock_data[symbol][day] * shares 
                    for symbol, shares in portfolio.items())
    portfolio_values.append(daily_value)

# Calculate daily returns
daily_returns = [
    (portfolio_values[i] - portfolio_values[i-1]) / portfolio_values[i-1] * 100
    for i in range(1, len(portfolio_values))
]

# Print results
print(f"Portfolio Values: {portfolio_values}")
print(f"Daily Returns (%): {daily_returns}")
print(f"Average Daily Return: {sum(daily_returns) / len(daily_returns):.2f}%")
print(f"Total Return: {(portfolio_values[-1] - portfolio_values[0]) / portfolio_values[0] * 100:.2f}%")
```

This program calculates the daily value of a portfolio and its returns using dictionaries, lists, and list comprehensions.

## Next Steps

In the next tutorial, we'll explore [Building a Simple Trading Algorithm](trading-algorithm.md) using the data structures we've learned about.