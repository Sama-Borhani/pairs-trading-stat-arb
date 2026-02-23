
# 📈 Pairs Trading Statistical Arbitrage Pipeline

> A fully reproducible research and backtesting framework for statistical arbitrage (pairs trading), built with transparency, diagnostic rigor, and portfolio-level accountability.

---

## Overview

This repository implements an end-to-end **pairs trading statistical arbitrage system**.
The objective is to:

1. Ingest historical equity price data
2. Clean and align time series
3. Generate candidate trading pairs
4. Filter pairs using cointegration and mean-reversion diagnostics
5. Generate trading signals (spread / z-score logic)
6. Reconstruct position-level PnL including transaction costs
7. Aggregate results into portfolio-level performance metrics
8. Export every intermediate artifact for auditability

The project is structured to make every stage inspectable and debuggable.

---

## Project Objective

The goal is not simply to produce returns. The goal is to build a **robust, explainable statistical arbitrage research pipeline** that:

* Avoids hidden data leakage
* Makes filtering logic explicit
* Allows reconstruction of returns from raw signals
* Identifies why candidate pairs fail or pass
* Produces clean research artifacts suitable for professional review

This is a research-grade backtesting workflow — not a black-box strategy script.

---

## Core Strategy Logic

### 1️⃣ Data Ingestion

* Historical adjusted close prices
* Saved as panel-style CSV
* Tickers as columns, dates as index

Example raw file:

```
data/raw/stock_prices_YYYY_MM_DD.csv
```

---

### 2️⃣ Cleaning & Alignment

We:

* Sort dates
* Remove tickers with excessive missing values
* Ensure sufficient historical overlap
* Align all series to common date index

This stage is critical because insufficient overlap leads to:

> “Not enough tickers / not enough pairs found”

---

### 3️⃣ Candidate Pair Generation

From the cleaned universe:

* Generate ticker combinations
* Enforce minimum overlap window
* Optionally restrict by sector (future enhancement)

---

### 4️⃣ Statistical Filtering

Pairs are filtered using diagnostics such as:

* Engle-Granger cointegration test
* Rolling ADF p-values
* Ljung-Box autocorrelation p-values
* Mean reversion fraction metrics
* Median rolling half-life
* Crossing frequency

Example diagnostics columns observed:

```
lb_pvalue_lag10
lb_pvalue_lag20
acf1
mr_fraction
mr_fraction_v2
median_half_life_roll
median_adf_p_roll
median_crossings_roll
```

These diagnostics are exported to:

```
results/statistics/
```

---

### 5️⃣ Signal Generation

For filtered pairs:

* Construct spread (log ratio or residual)
* Compute rolling z-score
* Entry threshold logic
* Exit threshold logic
* Position state tracking

This stage generates trade signals, not returns.

---

### 6️⃣ Cost-Aware Backtest

Signals are converted into:

* Position units
* Daily PnL
* Transaction cost deductions
* Net pair returns

Example artifact:

```
results/statistics/pair_returns_after_costs.csv
```

---

### 7️⃣ Portfolio Aggregation

Pair-level returns are aggregated into:

* Daily portfolio returns
* Performance statistics
* Final performance summary

Example outputs:

```
results/phase7/pair_returns_REBUILT_from_signals.csv
results/phase7/pair_returns_DAILY_from_pnl_spread_units.csv
results/phase7/portfolio_returns_REBUILT_daily.csv
results/phase7/portfolio_perf_stats_REBUILT_daily.csv
```

The “REBUILT” files ensure full reconstruction of returns from raw signals — eliminating hidden errors.

---

#  Major Issue Identified During Development

## Insufficient Surviving Pairs

A recurring issue:

> Too few tickers survive cleaning → too few candidate pairs → no statistically valid pairs.

Root causes identified:

* Overly strict missing-value filters
* Excessively tight cointegration thresholds
* Strict half-life bounds
* Insufficient historical overlap
* Data alignment issues
* Filename mismatch during ingestion (e.g. wildcard expectations)

Resolution approach:

* Print ticker count at every stage
* Print candidate pair count
* Track drop reasons
* Relax thresholds temporarily for diagnosis
* Standardize filename ingestion logic

---

# Repository Structure

```
pairs-trading-stat-arb/
│
├── README.md
├── LICENSE
├── .gitignore
├── requirements.txt
│
├── data/
│   ├── raw/              # Not committed (large / licensed data)
│   ├── sample/           # Small demo dataset (committed)
│   └── interim/
│
├── notebooks/
│   01_data_ingestion.ipynb
│   02_cleaning_alignment.ipynb
│   03_pair_generation.ipynb
│   04_cointegration_tests.ipynb
│   05_mean_reversion_diagnostics.ipynb
│   06_signal_generation.ipynb
│   07_backtest_portfolio.ipynb
│
├── src/
│   cleaning.py
│   pairs.py
│   tests.py
│   signals.py
│   backtest.py
│   portfolio.py
│
├── results/
│   ├── statistics/
│   ├── phase7/
│   └── figures/
│
└── docs/
    methodology.md
    troubleshooting.md
```

---

# Setup

## Requirements

* Python 3.10+
* pandas
* numpy
* statsmodels
* scipy
* matplotlib

Install:

```
pip install -r requirements.txt
```

---

## Google Colab Setup

Mount Drive:

```python
from google.colab import drive
drive.mount("/content/drive")
```

Set root:

```python
from pathlib import Path
PROJECT_ROOT = Path("/content/drive/MyDrive/pairs_trading_project")
```

---

# Research Outputs

The pipeline produces:

| Artifact                               | Purpose                         |
| -------------------------------------- | ------------------------------- |
| price_spikes_top5_each.csv             | Data quality diagnostics        |
| pair_returns_after_costs.csv           | Net daily returns per pair      |
| pair_returns_REBUILT_from_signals.csv  | Signal-to-return reconstruction |
| portfolio_returns_REBUILT_daily.csv    | Aggregated portfolio returns    |
| portfolio_perf_stats_REBUILT_daily.csv | Sharpe, drawdown, etc           |

All artifacts are exportable and auditable.

---

# Performance Metrics

Portfolio statistics include:

* Total Return
* Annualized Return
* Annualized Volatility
* Sharpe Ratio
* Max Drawdown
* Hit Rate
* Turnover (future enhancement)

---

# Next Steps (When Resuming Development)

### 1. Adding explicit drop-count logging at every stage

Track:

* tickers before cleaning
* tickers after cleaning
* candidate pairs
* pairs passing each filter

---

### 2. Building unified pair filtering report

Single CSV including:

* overlap length
* cointegration p-value
* half-life
* crossings
* final pass flag

---

### 3. Standardizing spread definition

Document clearly:

* log ratio vs residual
* regression method used
* hedge ratio estimation method

---

### 4. Strengthening cost modeling

Include:

* slippage
* borrow costs
* dynamic position sizing

---

### 5. Portfolio construction enhancements

Explore:

* Volatility targeting
* Exposure neutrality
* Max concurrent pair caps

---

### 6. Adding benchmark comparison

Compare against:

* SPY
* Equal-weight index
* Market-neutral baseline

---

### 7. Converting to full pipeline script

Create:

```
run_pipeline.py
```

to regenerate all outputs from raw data.

---

# Known Limitations

* Sensitive to universe size
* Sensitive to overlap window
* Sensitive to statistical threshold tuning
* No walk-forward validation implemented yet
* No out-of-sample validation yet

---

# License

MIT License


