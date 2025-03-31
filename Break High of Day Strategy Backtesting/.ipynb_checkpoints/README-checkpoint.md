# Break High of Day (BHOD) Strategy Backtesting

This strategy initiates a long position when a stock **breaks above the high of the day**, typically defined within a fixed intraday window. It aims to capture momentum-based intraday breakouts with proper risk management and partial exits.

---

## Strategy Overview

- **Entry Conditions:**  
  A trade is triggered when all of the following conditions are met:
    - The price breaks above the previous high of the day (HOD)
    - The previous two highs did not also break the previous HOD (no continuous high of day breaks)
    - The trade must occur between a pre-defined time range (default 09:35 - 15:55)
    - A pre-defined volume threshold is met

- **Stop Loss (SL):**  
  Placed at the **lower of the previous candle's midpoint** or the **current candle’s low**.

- **Take Profit (TP):**  
  Multiple take profit levels defined as `R-multiples` (risk-reward ratios), e.g., 1R, 2R, 3R.

- **Breakeven (BE):**  
  Created when pre-defined TP level is met; this acts as the new SL.

- **Exit Options:**  
  - Partial exits at each R-level
  - Stop-loss adjustment (original or breakeven)
  - Time-based exit (e.g., 3:55 PM)

---

## Key Parameters (Defaults)

| Parameter | Value / Description |
|----------|----------------------|
| **Trading Window** | 09:35 – 15:55 |
| **Risk per Trade (R)** | $20 |
| **Bid-Ask Spread** | $0.06 (slippage = 0.03) |
| **Max Trade Amount** | $50,000 |
| **Volume Condition Enabled** | Yes |
| **Volume Threshold** | Minimum 110% volume in a 14-period rolling window |

---

## Take Profit Logic (Defaults)

The position is scaled out using partial take profits at increasing R-multiples:

| Take Profit | Enabled | R Level | % of Position |
|-------------|---------|---------|----------------|
| 1st TP | ✅ | 1R | 25% |
| 2nd TP | ✅ | 2R | 25% |
| 3rd TP | ✅ | 3R | 50% |
| 4th TP | ❌ | – | 0% |

Additionally:

- **Stop-loss is moved to breakeven after 1st TP**
- No SL adjustment after 2nd or 3rd TP

---

## Data & Performance

- The strategy uses **1-minute OHLCV data** (Open, High, Low, Close, Volume).
- The dataset is **cleaned to ensure no missing timestamps** — every trading minute is accounted for during market hours.
- To scale across large historical datasets and multiple tickers, we use **Dask DataFrames** to parallelize processing and improve performance during backtesting.
- This makes the system suitable for testing strategies across **multi-year periods** and **high-resolution intraday data**.

---

## Fee & Cost Model (Defaults)

| Fee Type | Value |
|----------|-------|
| Commission per Share | $0.0035 |
| Minimum Commission per Order | $0.35 |
| Exchange Fees | $0.003 |
| Other Fees | $0.0005 |

---

## Position Sizing

- Shares are dynamically sized based on:
  - Entry price + slippage
  - SL distance
  - Max capital constraint
- Ensures that trade cost does not exceed the pre-defined max trade amount (`$50,000`)

---

## Outputs & Logs

Each backtest generates:
- A **trades table** with size, entry/exit info, PnL, duration, etc.
- Columns tracking whether each TP/SL was hit
- All relevant calculations for performance analysis

---

## Future Improvements

- Add short-side support
- Enhance volume filtering (e.g., relative volume, premarket scans)
- Optional pre-entry consolidation filter

---

## File Contents

This folder contains:
- Strategy logic implementation
- Parameter dictionary
- Trade simulator
- Result aggregation scripts

