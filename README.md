# IBEX 35 Statistical Arbitrage — Python

A pairs trading strategy built on cointegration analysis between Spanish equities,
focused on 7 IBEX 35 stocks. The pipeline covers the full workflow: data acquisition,
cointegration testing, signal generation, and backtesting with real-world execution
costs including the Spanish Financial Transaction Tax (FTT).

---

## A note on this project

An initial version of this notebook reported a Sharpe of 1.08 and a 121% return. After
sharing it, I audited my own backtest and found those numbers were inflated by
look-ahead bias and a position-sizing error. This repository contains the **corrected**
version. Each fix is marked in the notebook with a `# CORRECTION:` comment.

The corrected, out-of-sample result shows **no real edge** (Sharpe approximately 0).
That is the honest outcome, and the point of the project is the methodology: building a
backtest that doesn't fool you.

---

## Strategy Logic

Statistical arbitrage via pairs trading:

1. **Find cointegrated pairs** — stocks that share a long-term equilibrium relationship
2. **Calculate the spread** — the gap between the two stocks after adjusting for their
   dynamic hedge ratio
3. **Generate Z-score signals** — measure how abnormal the spread is vs its recent history
4. **Trade the reversion** — enter when the spread diverges beyond ±1.5 std deviations
5. **Exit at the mean** — close when the Z-score returns to 0

---

## What was corrected

| Bias | Problem | Fix |
|---|---|---|
| Pair-selection look-ahead | Pair chosen using the whole sample, then tested on it | Select pair on a separate formation period only |
| Hedge-ratio look-ahead | Rolling hedge ratio & z-score used same-day prices | Lag both by one day (`.shift(1)`) |
| Position sizing | Raw weights let daily returns exceed -100% in shocks | Normalise weights to gross exposure = 1 |
| Transaction costs | Slippage charged on one leg only | Slippage on both legs; FTT (0.2%) kept on the purchased leg |

Already correct in the original and kept: causal execution (`signals.shift(1)`) and
past-only rolling statistics.

---

## Project Structure

A single Jupyter Notebook in 6 steps:

| Step | Description |
|---|---|
| Step 1 | Data acquisition: download and clean price data via yFinance (formation + trading split) |
| Step 2 | Cointegration analysis: Engle-Granger test on the formation period |
| Step 3 | Rolling OLS & spread: dynamic hedge ratio and Z-score, lagged to avoid look-ahead |
| Step 4 | Signal generation: entry/exit rules based on Z-score thresholds |
| Step 5 | Backtesting: P&L with normalised sizing, slippage and Spanish FTT |
| Step 6 | Risk metrics & visualization: equity curve, drawdown, summary table |

---

## Key Parameters

| Parameter | Value |
|---|---|
| Pair | BBVA.MC / CABK.MC |
| Formation period | 2010-01-01 to 2019-06-30 (pair selection only) |
| Trading period | 2019-07-01 to 2021-12-31 (out-of-sample) |
| Rolling window | 60 trading days |
| Entry threshold | ±1.5 Z-score |
| Exit threshold | 0.0 Z-score |
| Slippage | 0.05% per leg |
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

---

## Key takeaway

Once the biases are removed, the strategy has no edge — which is the expected result for
standard pairs trading on a small blue-chip universe. The value here is methodological:
separating the data you select on from the data you test on, lagging every estimated
quantity, and sizing positions correctly. A backtest that lies is worse than no backtest.
