# Module 4: Hands-on — Microsoft Stock Analysis

## What We'll Build

In this module, we'll create a Python program that:

1. Downloads Microsoft (MSFT) stock price data for the past year
2. Downloads news related to Microsoft
3. Visualizes stock prices on a chart
4. Marks key events (news) on the chart
5. Helps understand why the price rose or fell

We'll do all of this with Claude Code — step by step.

## Setup

### Step 1: Create a Project Folder

```bash
mkdir stock-analysis
cd stock-analysis
claude
```

### Step 2: Ask Claude Code to Prepare the Environment

Type in Claude Code:

```
Create a Python virtual environment, a requirements.txt file
with yfinance, pandas, matplotlib, requests, and seaborn libraries.
Activate the environment and install the dependencies.
```

Claude Code will execute:
```bash
python -m venv venv
source venv/bin/activate  # On macOS/Linux
```

And create `requirements.txt`:
```
yfinance>=0.2.31
pandas>=2.1.0
matplotlib>=3.8.0
seaborn>=0.13.0
requests>=2.31.0
```

## Step 1: Download Stock Data

### Prompt for Claude Code

```
Create a file stock_analysis.py with the following function:

1. Function download_stock_data(ticker, period):
   - Downloads stock data by ticker (e.g., "MSFT")
     for the specified period (e.g., "1y" — one year)
   - Uses the yfinance library
   - Returns a DataFrame with columns: Date, Open, High, Low, Close, Volume
   - Prints to console: number of rows, first and last date,
     minimum and maximum closing price

2. In the if __name__ == "__main__" block, call this function for ticker MSFT
   for period 1y and print the first 5 rows
```

### What Claude Code Will Create

Claude Code will create the file `stock_analysis.py`. Here's the approximate code:

```python
import yfinance as yf
import pandas as pd


def download_stock_data(ticker: str, period: str = "1y") -> pd.DataFrame:
    """
    Downloads stock data.

    Args:
        ticker: Stock ticker (e.g., "MSFT")
        period: Data period ("1d", "5d", "1mo", "3mo", "6mo", "1y", "2y", "5y")

    Returns:
        DataFrame with stock data
    """
    print(f"Downloading data for {ticker} for period {period}...")

    stock = yf.Ticker(ticker)
    df = stock.history(period=period)

    # Reset index so Date becomes a regular column
    df = df.reset_index()

    # Remove timezone from date for convenience
    df["Date"] = pd.to_datetime(df["Date"]).dt.tz_localize(None)

    print(f"Loaded {len(df)} rows")
    print(f"Period: {df['Date'].min().strftime('%Y-%m-%d')} — {df['Date'].max().strftime('%Y-%m-%d')}")
    print(f"Closing price: min ${df['Close'].min():.2f}, max ${df['Close'].max():.2f}")

    return df


if __name__ == "__main__":
    df = download_stock_data("MSFT", period="1y")
    print("\nFirst 5 rows:")
    print(df.head())
```

### Running

Ask Claude Code to run the script:

```
Run stock_analysis.py
```

You'll see something like:
```
Downloading data for MSFT for period 1y...
Loaded 251 rows
Period: 2024-02-14 — 2025-02-13
Closing price: min $385.25, max $468.35

First 5 rows:
         Date        Open        High  ...      Close    Volume
0  2024-02-14  409.970001  412.480011  ...  409.489990  23456789
1  2024-02-15  411.200012  414.079987  ...  413.640015  19876543
...
```

## Step 2: Visualize Stock Prices

### Prompt for Claude Code

```
Add a function plot_stock_price(df, ticker) to stock_analysis.py:
- Plot a beautiful closing price chart
- Figure size 14x7
- Blue line, thickness 1.5
- Add a grid with transparency 0.3
- Title: "{ticker} Stock Price Over the Past Year"
- Axis labels: "Date" and "Price (USD)"
- Add moving averages: MA50 (red line) and MA200 (green line)
- Add a legend
- Save to stock_price.png at 150 dpi
- Call the function in the main block
```

### Result

Claude Code will add the visualization function:

```python
import matplotlib.pyplot as plt
import matplotlib.dates as mdates


def plot_stock_price(df: pd.DataFrame, ticker: str) -> None:
    """Plots stock price chart with moving averages."""

    fig, ax = plt.subplots(figsize=(14, 7))

    # Closing price
    ax.plot(df["Date"], df["Close"], color="#2196F3", linewidth=1.5, label="Closing Price")

    # Moving averages
    df["MA50"] = df["Close"].rolling(window=50).mean()
    df["MA200"] = df["Close"].rolling(window=200).mean()

    ax.plot(df["Date"], df["MA50"], color="#F44336", linewidth=1,
            linestyle="--", label="MA50 (50 days)", alpha=0.8)
    ax.plot(df["Date"], df["MA200"], color="#4CAF50", linewidth=1,
            linestyle="--", label="MA200 (200 days)", alpha=0.8)

    # Formatting
    ax.set_title(f"{ticker} Stock Price Over the Past Year", fontsize=16, fontweight="bold")
    ax.set_xlabel("Date", fontsize=12)
    ax.set_ylabel("Price (USD)", fontsize=12)
    ax.legend(fontsize=10)
    ax.grid(True, alpha=0.3)

    # Date format
    ax.xaxis.set_major_formatter(mdates.DateFormatter("%b %Y"))
    ax.xaxis.set_major_locator(mdates.MonthLocator(interval=2))
    plt.xticks(rotation=45)

    plt.tight_layout()
    plt.savefig("stock_price.png", dpi=150, bbox_inches="tight")
    print("Chart saved to stock_price.png")
    plt.show()
```

## Step 3: Add Trading Volume

### Prompt for Claude Code

```
Add a function plot_price_and_volume(df, ticker) that creates
two charts stacked vertically (subplots):
- Top: closing price (as before, with moving averages)
- Bottom: trading volume as a bar chart
  - Green bars: days when the price went up
  - Red bars: days when the price went down
- Save to price_and_volume.png
```

### Result

Claude Code will create a professional dual chart:

```python
def plot_price_and_volume(df: pd.DataFrame, ticker: str) -> None:
    """Plots price and trading volume chart."""

    fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(14, 10),
                                     height_ratios=[3, 1], sharex=True)

    # Top chart — price
    ax1.plot(df["Date"], df["Close"], color="#2196F3", linewidth=1.5, label="Closing Price")

    ma50 = df["Close"].rolling(window=50).mean()
    ax1.plot(df["Date"], ma50, color="#F44336", linewidth=1,
             linestyle="--", label="MA50", alpha=0.8)

    ax1.set_title(f"{ticker} Stock: Price and Trading Volume", fontsize=16, fontweight="bold")
    ax1.set_ylabel("Price (USD)", fontsize=12)
    ax1.legend(fontsize=10)
    ax1.grid(True, alpha=0.3)

    # Bottom chart — volume
    colors = ["#4CAF50" if df["Close"].iloc[i] >= df["Open"].iloc[i]
              else "#F44336" for i in range(len(df))]

    ax2.bar(df["Date"], df["Volume"], color=colors, alpha=0.7, width=1)
    ax2.set_ylabel("Volume", fontsize=12)
    ax2.set_xlabel("Date", fontsize=12)
    ax2.grid(True, alpha=0.3)

    plt.tight_layout()
    plt.savefig("price_and_volume.png", dpi=150, bbox_inches="tight")
    print("Chart saved to price_and_volume.png")
    plt.show()
```

## Step 4: Add Key Events

### Prompt for Claude Code

```
Add a function add_key_events(df, ticker):
- Define a list of key Microsoft events from the past year manually
  (e.g., quarterly earnings release, Copilot announcement, OpenAI partnership, etc.)
- Each event: date, short description, type (positive/negative)
- Draw a stock price chart
- Mark each event with a vertical line:
  - Green line + up arrow for positive events
  - Red line + down arrow for negative events
- Add rotated text labels (45 degrees)
- Save to stock_events.png
```

### Result — Events on the Chart

```python
def add_key_events(df: pd.DataFrame, ticker: str) -> None:
    """Draws stock price chart with key events."""

    # Key Microsoft events (examples)
    events = [
        {"date": "2024-04-25", "text": "Q3 Earnings\n(revenue +17%)", "type": "positive"},
        {"date": "2024-05-20", "text": "Copilot+\nfor Windows", "type": "positive"},
        {"date": "2024-07-23", "text": "Q4 Earnings\n(cloud slowed)", "type": "negative"},
        {"date": "2024-09-10", "text": "AI Infrastructure\nInvestment", "type": "positive"},
        {"date": "2024-10-30", "text": "Q1 Earnings\n(Azure growth 34%)", "type": "positive"},
        {"date": "2025-01-29", "text": "Q2 Earnings\n(forecast below expectations)", "type": "negative"},
    ]

    fig, ax = plt.subplots(figsize=(16, 8))

    # Price
    ax.plot(df["Date"], df["Close"], color="#2196F3", linewidth=1.5, label="Closing Price")
    ax.fill_between(df["Date"], df["Close"].min() * 0.98, df["Close"], alpha=0.1, color="#2196F3")

    # Events
    for event in events:
        event_date = pd.to_datetime(event["date"])

        # Check that the date is in our range
        if event_date < df["Date"].min() or event_date > df["Date"].max():
            continue

        color = "#4CAF50" if event["type"] == "positive" else "#F44336"

        # Vertical line
        ax.axvline(x=event_date, color=color, linestyle="--", alpha=0.5, linewidth=1)

        # Label
        y_pos = df["Close"].max() * 1.02 if event["type"] == "positive" else df["Close"].min() * 0.98
        ax.annotate(
            event["text"],
            xy=(event_date, y_pos),
            fontsize=8,
            color=color,
            fontweight="bold",
            ha="center",
            rotation=0,
            bbox=dict(boxstyle="round,pad=0.3", facecolor="white", edgecolor=color, alpha=0.8),
        )

    ax.set_title(f"{ticker} Stock: Price and Key Events", fontsize=16, fontweight="bold")
    ax.set_xlabel("Date", fontsize=12)
    ax.set_ylabel("Price (USD)", fontsize=12)
    ax.legend(fontsize=10)
    ax.grid(True, alpha=0.3)

    plt.tight_layout()
    plt.savefig("stock_events.png", dpi=150, bbox_inches="tight")
    print("Chart with events saved to stock_events.png")
    plt.show()
```

## Step 5: Putting It All Together

### Prompt for Claude Code

```
Assemble all the code into the final version of stock_analysis.py:
- All 4 functions
- A nice main block with a menu:
  1. Download data
  2. Price chart
  3. Price + volume chart
  4. Chart with events
  5. Show statistics
  6. Exit
- Add error handling
- Add colored console output
```

## What We Learned from the Charts

The charts reveal typical patterns:

### Why the Price Rises:
- **Strong quarterly reports** — when revenue and profit beat analyst expectations
- **Cloud business growth (Azure)** — investors believe in the future of cloud services
- **AI products (Copilot)** — new AI-based products generate interest

### Why the Price Falls:
- **Weak forecasts** — even with good current results, if the forecast is weak — the price drops
- **Growth slowdown** — if Azure grows "only" 29% instead of the expected 31%
- **Macroeconomics** — interest rates, inflation, geopolitics affect all stocks

### Moving Averages:
- **MA50 above MA200** — bullish signal (price is likely to rise)
- **MA50 below MA200** — bearish signal (price is likely to fall)
- **MA50 crossing MA200** — "golden cross" or "death cross" — an important signal

## Homework

Try these with Claude Code:

1. **Switch the ticker** — analyze another company (AAPL, GOOGL, NVDA)
2. **Add a comparison** — plot two stocks on one chart (normalized)
3. **Add an RSI indicator** — ask Claude Code to add the RSI technical indicator
4. **Export to HTML** — ask Claude Code to create an interactive chart with plotly

## Module Summary

- We created a complete stock analysis program step by step
- Claude Code helped at every stage: from creating files to running code
- Each prompt was specific and described the expected result
- We learned to visualize data and add context (events) to charts

---

[← Previous: Claude Code Capabilities](03-claude-code-capabilities.md) | [Back to Course](README.md) | [Next: More Examples →](05-more-examples.md)
