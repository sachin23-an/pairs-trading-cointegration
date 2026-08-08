# Statistical Pairs Trading — Engle-Granger Cointegration and Z-Score Mean Reversion

## Research Question

Do HDFC Bank and ICICI Bank — two similar, sector-matched Indian private banks — show a genuine statistical basis (cointegration, not just correlation) for a mean-reversion pairs trade, and does that basis actually hold up out-of-sample, using a hedge ratio estimated only on earlier data?

## What's in this repo

| File | Description |
|---|---|
| `pairs_trading_cointegration.ipynb` | The full notebook — data, cointegration test, hedge ratio, spread, z-score signal, walk-forward split, out-of-sample backtest, conclusion |
| `chart_01_price_history.png` | HDFC Bank vs ICICI Bank real price history |
| `chart_02_spread_full_sample.png` | The spread using the full-sample hedge ratio (illustration only — not the actual backtest) |
| `chart_03_backtest_results.png` | Z-score, position, and cumulative P&L over the out-of-sample test period |
| `README.md` | This file |

## Data — real only, no synthetic fallback

This notebook pulls real daily closing prices for HDFCBANK.NS and ICICIBANK.NS via `yfinance`. **There is no synthetic data anywhere in this notebook.** If the fetch fails (no internet), the notebook raises a clear error and stops.

## Why this pair

Pairs trading only makes sense for two stocks that are genuinely economically linked — same sector, similar business drivers. HDFC Bank and ICICI Bank are both large Indian private-sector banks, which is a reasonable, disclosed starting candidate — not a claim that this is the single best pair available.

## Method, in order

1. **Correlation vs cointegration, explained plainly** — correlation alone (two stocks generally trending up together) is not sufficient basis for this strategy; cointegration (a stationary combination of the two prices) is.
2. **Engle-Granger cointegration test**, via `statsmodels`' `coint` function — used as a gate, not a formality. If the pair fails this test, that's reported honestly rather than proceeding anyway.
3. **Hedge ratio** from an OLS regression of one price on the other.
4. **Z-score of the spread**, using a rolling window, converted into a simple, explicit entry/exit trading rule.
5. **An honest walk-forward split**: the data is split into a training period (60%) and testing period (40%). The hedge ratio and cointegration test are re-run using *only* the training period, and that hedge ratio is the only one used in the actual backtest — it is never re-fit on the test period. This directly avoids the most common leakage mistake in pairs trading backtests.
6. **Out-of-sample backtest**, with transaction costs applied per trade.

## Honest conclusion

This notebook demonstrates the real statistical basis for pairs trading and a backtest structured to avoid the most common leakage mistake specific to this strategy type (re-fitting the hedge ratio on the same data used to test it).

**What it doesn't claim:**
1. **One pair, one period** — real pairs desks screen dozens or hundreds of candidates and expect most to fail; this doesn't claim HDFC Bank / ICICI Bank is uniquely special.
2. **Cointegration can break down** — a relationship that held in the training period isn't guaranteed to hold forever; a real deployment would need ongoing re-testing.
3. **A fixed hedge ratio is a simplification** — some desks use a rolling or Kalman-filter-updated ratio instead of a single static one; using a static ratio here is disclosed, not hidden.

The defensible finding is the *methodology* — testing for cointegration before trading a pair, and validating the hedge ratio out-of-sample — not a claim that this specific pair is a proven, ready-to-deploy strategy.

## How to run

Fully Colab-compatible. No local setup, no paid data, no GPU required.

```bash
pip install yfinance statsmodels --quiet
```

Then run all cells top to bottom. Since there is no synthetic fallback, an internet connection is required — Colab has one by default.

## Requirements

```
numpy
pandas
matplotlib
statsmodels
yfinance
```
