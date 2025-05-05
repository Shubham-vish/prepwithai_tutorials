# Getting Started with Python

Python is a versatile, high-level programming language known for its readability and simplicity. It's widely used in data science, web development, automation, and financial analysis. This tutorial will help you get started with Python programming.

## Installation

Before you can start coding, you need to install Python on your system.

### Windows

1. Download the installer from [python.org](https://www.python.org/downloads/)
2. Run the installer and make sure to check "Add Python to PATH"
3. Verify the installation by opening Command Prompt and typing:

```python
python --version
```

### Mac OS

Mac OS comes with Python pre-installed, but it's often an older version. To install the latest:

1. Install Homebrew (if not already installed):

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

2. Install Python using Homebrew:

```bash
brew install python
```

### Linux

Most Linux distributions come with Python pre-installed. If not, you can install it using your distribution's package manager:

For Ubuntu/Debian:
```bash
sudo apt update
sudo apt install python3 python3-pip
```

For Fedora:
```bash
sudo dnf install python3 python3-pip
```

## Hello World

Let's write our first Python program. Create a file named `hello.py` with the following content:

```python
# This is a comment
print("Hello, World!")

# Variables
name = "Trader"
print(f"Hello, {name}!")
```

To run this program:

```bash
python hello.py
```

Expected output:
```
Hello, World!
Hello, Trader!
```

## Basic Python Syntax

Python uses indentation (whitespace) to define code blocks, rather than braces or keywords:

```python
# Conditional statement
price = 150

if price > 200:
    print("Price is high")
elif price > 100:
    print("Price is medium")
    if price > 150:
        print("On the higher end of medium")
else:
    print("Price is low")
```

Running this code will output:
```
Price is medium
On the higher end of medium
```

## Functions in Python

Functions in Python are defined using the `def` keyword:

```python
def calculate_moving_average(prices, window=5):
    """
    Calculate the moving average of a list of prices.
    
    Args:
        prices (list): List of price values
        window (int): The window size for the moving average
    
    Returns:
        list: The moving averages
    """
    results = []
    
    for i in range(len(prices) - window + 1):
        window_average = sum(prices[i:i+window]) / window
        results.append(window_average)
    
    return results

# Example usage
stock_prices = [100, 102, 104, 103, 105, 107, 108, 106]
ma = calculate_moving_average(stock_prices)
print(f"Moving averages: {ma}")
```

Output:
```
Moving averages: [102.8, 104.2, 105.4, 105.8, 106.5]
```

## Interactive Mode

Python can be used interactively as a calculator. Open your terminal or command prompt and type `python` or `python3`:

```python
>>> 2 + 2
4
>>> 10 / 3
3.3333333333333335
>>> name = "Python"
>>> f"I'm learning {name}"
"I'm learning Python"
```

## Next Steps

Now that you've learned the basics, you can move on to [Data Structures in Python](data-structures.md) to learn about lists, dictionaries, sets, and tuples.

Practice makes perfect! Try to create small programs and explore the Python documentation at [docs.python.org](https://docs.python.org/).