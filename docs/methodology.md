# Methodology

## Spread Construction
Spread defined as log price ratio between paired assets.

## Cointegration Testing
Engle-Granger test applied.
Pairs retained if p-value < threshold.

## Mean Reversion Diagnostics
- Rolling ADF
- Half-life estimation
- Crossing frequency
- Ljung-Box p-values

## Signal Logic
- Entry when |z-score| exceeds threshold
- Exit when z-score reverts to mean

## Backtest
- Daily position tracking
- Transaction cost deduction
- Portfolio equal-weight aggregation
