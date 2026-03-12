# Testing Order Flow Imbalance in Bitcoin Markets

## Overview
We backtest and stress-test a high-frequency trading (HFT) strategy based on Order Flow Imbalance (OFI). The analysis evaluates tick-level order book data across six major cryptocurrency exchanges to determine if theoretical OFI alpha can survive real-world market microstructure frictions.

## Data Preprocessing
To accurately compare order flow across fragmented liquidity pools, the raw trade data is processed through three systematic steps:

* **Unit Normalization:** Standardizes contract sizes, specifically converting Deribit's inverse futures contracts into standard underlying BTC quantities.
* **Winsorization:** Clips extreme outliers (1st and 99th percentiles) from the raw flow to prevent anomalous, massive block trades from distorting the underlying signal.
* **Z-Score Standardization:** Normalizes the signal using a rolling window. This ensures the model measures signal strength by its relative deviation from the mean, rather than its absolute volume.

## Core Strategy & Execution
The strategy transforms the normalized order flow into actionable trade decisions via a chronological, three-step framework:

1. **Target Construction:** Calculates the "Forward Return" over a specified horizon (defaulting to a 1-second interval) to establish the target variable the model will attempt to predict.
2. **Model Training:** Executes a strict chronological split (40% Train / 60% Test). A Linear Regression is fitted on the training set to derive the Beta coefficient, quantifying the OFI signal's predictive power.
3. **Dynamic Thresholding:** The derived Beta is applied to the out-of-sample Test set. Trades are conditionally triggered only if the absolute predicted return exceeds a dynamic "J-Threshold," which is dynamically calibrated to target a specific market participation rate (e.g., executing only on the top 5% of opportunities).

## Conclusion
Our backtest reveals a high-alpha signal that is ultimately undermined by extreme directional risk and a lack of cross-venue generalizability.

* Under optimized settings, the strategy generated a Net P&L of $575,712 on OKX.
* On that exact same exchange, the Maximum Drawdown reached -$1.2M, more than double the total generated profit.
* The strategy successfully enters trades based on a 1-second predictive horizon but lacks a formal exit or stop-loss framework. Without position limits, the strategy suffered a catastrophic **-$62.3M drawdown** by mechanically "averaging down" into persistent, losing market trends
