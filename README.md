# Testing Order Flow Imbalance in Bitcoin Markets

## Overview
This project stress-tests a high-frequency trading (HFT) strategy based on Order Flow Imbalance (OFI) across six major cryptocurrency exchanges. 

## Data Preprocessing
Raw trade data is processed through a three-step pipeline to handle fragmented liquidity:
1. **Unit Normalization:** Converts Deribit's inverse futures into standard BTC quantities.
2. **Winsorization:** Clips extreme outliers (1st/99th percentiles) to prevent block trades from distorting the signal.
3. **Z-Score Standardization:** Normalizes the signal via a rolling window to measure relative deviation from the mean.

## Strategy Logic
The strategy translates order flow into trade decisions systematically:
1. **Target Construction:** Calculates the "Forward Return" over a set horizon (e.g., 1 second).
2. **Model Training:** Splits data chronologically (40% Train / 60% Test) and fits a Linear Regression to derive a predictive Beta coefficient.
3. **Dynamic Thresholding:** Applies the Beta to the out-of-sample set. Trades trigger only if the predicted return exceeds a dynamic "J-Threshold" (targeting the top 5% of opportunities).

## Conclusion: Performance & Risk
The backtest reveals a high-alpha signal severely undermined by directional risk and scaling limits.
* **Drawdown Risk:** On OKX, optimized settings generated a Net P&L of $575,712. However, the Maximum Drawdown was -$1.2M.
* **Missing Exit Logic:** Without a formal stop-loss, the model averaged down into losing trends, ultimately suffering a catastrophic -$62.3M drawdown.
* **Scalability Frictions:** Real-world network latency inevitably causes slippage, destroying HFT margins. Furthermore, scaling position limits (e.g., from 10 BTC to 100 BTC) hits a strict liquidity ceiling, triggering immediate adverse selection.

## Data Notice
> The raw tick and order book data used for this analysis are stored in massive `.parquet` files that exceed GitHub's repository limits. To comply with these size constraints, the underlying data files have been excluded from this repository. 
