# IBEX 35 Statistical Arbitrage — Python

A fully automated pairs trading strategy built in Python, focused on the 7 most
relevant Spanish equities in the IBEX 35. The pipeline covers the full workflow:
from data acquisition to backtesting, accounting for real-world execution costs
including the Spanish Financial Transaction Tax (FTT).

---

## Strategy Logic

The strategy is based on **statistical arbitrage** — specifically pairs trading:

1. **Find cointegrated pairs**: stocks that move together long-term
2. **Calculate the spread**: the gap between the two stocks after adjusting for their dynamic hedge ratio
3. **Generate Z-score signals**: measure how abnormal the spread is relative to its recent history
4. **Trade the reversion**: when the spread diverges beyond ±1.5 standard deviations, bet it comes back to normal
5. **Exit at the mean**: close the position when the Z-score returns to 0

---

## Results

Tested on the **IBE.MC / TEF.MC** pair (Iberdrola / Telefónica) from 2019 to 2021:

| Metric | Value |
|---|---|
| Total Return | 121.78% |
| Sharpe Ratio | 1.08 |
| Max Drawdown | -29.06% |
| Days in Market | ~42% of trading days |

The strategy significantly outperformed buying either stock individually over the same period.

---

## Project Structure

The project is built as a single Jupyter Notebook divided into 6 steps:

| Step | Description |
|---|---|
| Step 1 | Data Acquisition: download and clean price data via yFinance |
| Step 2 | Cointegration Analysis: identify tradeable pairs using Engle-Granger test |
| Step 3 | Rolling OLS & Spread Calculation: dynamic hedge ratio and Z-score |
| Step 4 | Signal Generation: entry and exit rules based on Z-score thresholds |
| Step 5 | Backtesting: P&L simulation with slippage and Spanish FTT |
| Step 6 | Risk Metrics & Visualization: equity curve, drawdown, and summary table |

---

## Key Parameters

| Parameter | Value |
|---|---|
| Tickers | IBE.MC / TEF.MC |
| Date Range | 2019-01-01 to 2021-12-31 |
| Rolling Window | 60 trading days |
| Entry Threshold | ±1.5 Z-score |
| Exit Threshold | 0.0 Z-score |
| Slippage | 0.05% per trade |
| Spanish FTT | 0.20% on purchases |

---

## Requirements
```
yfinance
pandas
numpy
matplotlib
statsmodels
```

## Lessons Learned

The strategy performed well during 2019–2021 but broke down in the post-COVID period as macroeconomic shocks (inflation, rate hikes, energy crisis) permanently altered the relationship between Iberdrola and Telefónica. This highlights a fundamental truth of statistical arbitrage:

> *Cointegration is not permanent — regime monitoring is essential in real-world deployment.*
