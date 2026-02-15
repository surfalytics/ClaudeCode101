# Module 4: Hands-on — Microsoft Stock Analysis

## What We'll Build

In this module, we'll create a Python program that:

1. Downloads Microsoft (MSFT) stock price data for the past year
2. Visualizes stock prices on a chart
3. Marks key events on the chart
4. Helps understand why the price rose or fell

This is a classic data professional task — pulling data, transforming it, and visualizing insights.

## Setup

```bash
mkdir stock-analysis
cd stock-analysis
claude
```

Ask Claude Code to prepare the environment:

```
Create a Python virtual environment, a requirements.txt file
with yfinance, pandas, matplotlib, requests, and seaborn libraries.
Activate the environment and install the dependencies.
```

## Step 1: Download Stock Data

```
Create a file stock_analysis.py with the following function:

1. Function download_stock_data(ticker, period):
   - Downloads stock data by ticker (e.g., "MSFT")
     for the specified period (e.g., "1y" — one year)
   - Uses the yfinance library
   - Returns a DataFrame

2. In the if __name__ == "__main__" block, call the function for MSFT ticker
```

Claude Code will create a file with the complete code:

```python
import yfinance as yf
import pandas as pd

def download_stock_data(ticker: str, period: str = "1y") -> pd.DataFrame:
    """Downloads stock data."""
    stock = yf.Ticker(ticker)
    df = stock.history(period=period)
    df = df.reset_index()
    df["Date"] = pd.to_datetime(df["Date"]).dt.tz_localize(None)
    return df

if __name__ == "__main__":
    df = download_stock_data("MSFT", period="1y")
    print(df.head())
```

## Step 2: Visualize Stock Prices

```
Add a function plot_stock_price(df, ticker) to stock_analysis.py:
- Plot the closing stock price
- Add moving averages: MA50 and MA200
- Save to stock_price.png
```

## Step 3: Add Trading Volume

```
Add a function plot_price_and_volume(df, ticker) —
two charts stacked vertically: price and trading volume.
```

## Step 4: Add Key Events

```
Add a function add_key_events(df, ticker):
- List of key Microsoft events
- Vertical lines on the chart (green — positive, red — negative)
```

## What We Learned from the Charts

**Why the price rises:**
- Strong quarterly reports
- Cloud business growth (Azure)
- AI products (Copilot)

**Why the price falls:**
- Weak forecasts
- Growth slowdown
- Macroeconomic factors

**Moving averages:**
- MA50 above MA200 — bullish signal
- MA50 below MA200 — bearish signal
- MA50 crossing MA200 — "golden cross" or "death cross"

## Homework

1. Switch the ticker — analyze AAPL, GOOGL, NVDA
2. Add a comparison of two stocks on one chart
3. Add an RSI indicator
4. Export to HTML using plotly

## Module Summary

- We created a complete stock analysis program step by step
- Claude Code helped at every stage
- Each prompt was specific and described the expected result

---

[← Previous: Claude Code Capabilities](03-claude-code-capabilities.md) | [Back to Course](README.md) | [Next: More Examples →](05-more-examples.md)
