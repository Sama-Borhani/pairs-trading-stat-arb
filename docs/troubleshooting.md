# Troubleshooting

## Problem: No pairs found

Likely causes:
- Too few tickers after cleaning
- Insufficient overlap window
- Too strict cointegration threshold
- Missing data alignment issues

## Problem: Colab mount error

Restart runtime and mount:
drive.mount("/content/drive")

## Problem: Zero returns

Check:
- Signals generated?
- Positions updating?
- Costs exceeding gross returns?
