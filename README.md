# Dynamic Value-at-Risk (VaR) Forecasting via GARCH(1,1) Engines

## Executive Summary
This project implements an institutional-grade quantitative risk framework utilizing conditional variance modeling to estimate and backtest Daily Value-at-Risk (VaR) thresholds. By moving away from static historical variance models, this pipeline deploys a **GARCH(1,1)** framework to capture daily **Volatility Clustering** and regime transformations, actively protecting portfolio capital during structural market shocks.

## The Econometric Framework
Standard risk models assume *homoskedasticity* (constant variance over time), which fails catastrophically during financial crises. This framework models asset returns as a time-varying conditional variance process:

1. **Mean Equation:** $$R_t = \mu + \varepsilon_t$$
2. **Conditional Variance Equation:** $$\sigma_t^2 = \omega + \alpha \varepsilon_{t-1}^2 + \beta \sigma_{t-1}^2$$

Where $\alpha \varepsilon_{t-1}^2$ represents the immediate market innovation (ARCH term / news effect) and $\beta \sigma_{t-1}^2$ captures the variance memory persistence (GARCH term).

## Pipeline Architecture & Calibration
* **Asset Universe:** Historical daily data for the benchmark **NIFTY 50 Index** (2,560+ trading days).
* **Data Transformation:** Processed raw non-stationary closing prices into daily **Log Percentage Returns** ($R_t = \ln(P_t / P_{t-1}) \times 100$) to stabilize the Maximum Likelihood Estimation (MLE) calculations.
* **Stationarity Constraints:** The model successfully converged to a strictly stationary, mean-reverting path satisfying the constraint: $\alpha + \beta < 1$.
  * Estimated Parameters: $\omega = 0.0237$, $\alpha = 0.1059$, $\beta = 0.8698$ ($\alpha + \beta = 0.9757$).
  * High persistence ($\beta = 0.8698$) establishes that volatility shocks decay slowly and realistically across long horizons.

## Risk Backtesting & Empirical Validation
* **Risk Horizon:** 95% Daily One-Sided Value-at-Risk boundary ($\text{VaR}_t = \mu - 1.645 \times \sigma_t$).
* **Backtest Results:** Across 2,561 trading days, the model experienced exactly **122 empirical risk breaches**, resulting in a historical breach ratio of **4.76%**—aligning near-perfectly with the theoretical **5.00%** failure target.
* **Structural Observation:** The framework captured the historic **March 2020 systemic crash**, instantly dropping its risk threshold to nearly $-10\%$ to insulate capital reserves before gracefully decaying as market panic subsided.
