# SMA Crossover Backtest on Crypto (BTC, ETH, XRP)

A backtest of a moving-average crossover strategy on Bitcoin, Ethereum and XRP. Parameters are chosen on earlier data, then tested on later data the selection never saw.

Group project for the Master of Science in Quantitative Finance at Singapore Management University (SMU).

## Key results

Test period: 2022-01-01 to 2026-09-09 (1,713 days), 10 basis points (0.10%) transaction cost.

| | Strategy (equal-weight BTC, ETH, XRP) | Buy-and-hold basket (equal-weight) |
|---|---|---|
| Total return | +41.1% | +57.5% |
| Sharpe ratio | 0.21 | 0.16 |
| Annual volatility | 35% | 60% |
| Max drawdown | -49.7% | -65.8% |

- The strategy earned less than simply holding the basket, but with lower risk.
- Alpha (return not explained by market moves) is +1.8% a year with a t-statistic of 0.15. This is not statistically different from zero.
- Results are sensitive to costs: the strategy's test-period return falls from +61.7% with no cost to +14.9% at 25 bps and -18.3% at 50 bps.

| Coin | Strategy (test) | Buy-and-hold (test) |
|---|---|---|
| BTC | +20.9% | +68.8% |
| ETH | -26.4% | -33.0% |
| XRP | +77.7% | +67.8% |

Plain-language terms:
- **Sharpe ratio**: return earned per unit of risk. Higher is better.
- **Max drawdown**: the largest drop from a peak to a later low.
- **Alpha / beta**: how much of the return comes from skill (alpha) versus moving with the market (beta).
- **t-statistic**: how strong the evidence is. Around 2 or above is usually treated as significant.
- **Basis point (bp)**: 0.01%.

## What the strategy does

- Each day, compare a short moving average of the price with a long one.
- Be invested (long) when the short average is above the long average, otherwise hold cash (flat).
- The position is shifted by one day, so each day's signal only uses information available before that day.
- Five short/long window pairs are tested: 3/10, 5/20, 10/50, 20/100 and 50/200 days.

## Method

1. **Split the data.** Fit window: 2016-02-23 to 2021-12-31. Test window: 2022-01-01 onward.
2. **Choose the windows per coin on the fit window only.** Candidates are ranked on total return (weight 0.3), Sharpe ratio (0.5) and max drawdown (0.2). Selected: BTC 5/20, ETH 5/20, XRP 3/10.
3. **Backtest on the test window** with a 10 bp transaction cost (set by `COST_BPS` in the notebook).
4. **Combine** the three coin strategies into an equal-weight portfolio and compare with an equal-weight buy-and-hold basket.
5. **Test for alpha** with a market regression (CAPM) using Newey-West standard errors (a correction for returns that are correlated from day to day), 5 lags, a 2.54% annual risk-free rate and 365-day annualisation.
6. **Check cost sensitivity** at 0, 5, 10, 25 and 50 bps.

## Repository contents

| File | Description |
|---|---|
| `sma_backtest_fixed.ipynb` | The full analysis |
| `close_price_panel_full_unbalanced.csv` | Daily closing prices for BTC, ETH and XRP |

## How to run

1. Install Python 3 and the packages: `pip install pandas numpy statsmodels lets-plot jupyter`
2. Open the notebook with `jupyter notebook sma_backtest_fixed.ipynb`.
3. Choose **Kernel > Restart & Run All**. Keep the CSV in the same folder as the notebook.

## Limitations

- Only three coins and five window pairs were tested.
- The alpha estimate is not significant, so there is no evidence the strategy beats the market after costs.
- Results depend heavily on the cost assumption. A single flat cost per trade ignores spreads and price impact.
- The test window is one market period, so results could differ in other conditions.
- The full-history tables in the later part of the notebook mix fit and test data. They describe the data but are not out-of-sample results.

## Authors

- Eric Jansen
- Davidson Joel Mundt
- Diyaa Rajeev Valiyaparambath Kuttikattil
