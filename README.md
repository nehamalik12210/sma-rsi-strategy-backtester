# Trading Strategy Backtester (SMA Crossover + RSI Mean-Reversion)

Walk-forward backtest of two long-only strategies on 5 NSE large-caps, with transaction costs and
statistical significance testing.

## Strategies

- **SMA Crossover** — long when 20-day SMA > 50-day SMA (params optimized per fold)
- **RSI Mean-Reversion** — long on RSI oversold, exit on recovery

## Method

- Data: daily OHLC for `RELIANCE.NS`, `TCS.NS`, `HDFCBANK.NS`, `INFY.NS`, `ICICIBANK.NS` via `yfinance`
- 4-fold walk-forward validation (expanding train window, non-overlapping test folds)
- Parameters re-optimized on train data only, evaluated on unseen test data
- 10 bps transaction cost per trade
- t-test + bootstrap 95% CI on Sharpe ratio, per fold

## Results (Jan 2019 – Aug 2026, 1,882 trading days)

| Strategy | Mean Sharpe | Std Sharpe | % Folds Positive | % Significant (p<0.05) |
|---|---|---|---|---|
| SMA Crossover | -0.70 | 1.07 | 30% | 0% |
| RSI Mean-Reversion | -0.26 | 0.73 | 40% | 0% |

Neither strategy shows a statistically significant edge — 0/40 ticker-fold combinations had a
Sharpe CI excluding zero. RSI is directionally more consistent than SMA but still underperforms
buy-and-hold on average.

Exception: in the most recent fold, both strategies beat buy-and-hold on TCS, HDFC Bank, and
Infosys during the Jan–Mar 2026 drawdown, by staying in cash instead of holding through the drop.

## Files

- `ma_crossover_backtest.ipynb` — main notebook, run top to bottom
- `requirements.txt` — dependencies
- `walkforward_results.csv`, `strategy_comparison_summary.csv` — generated on run

## Run it

**Locally**
```bash
pip install -r requirements.txt
jupyter notebook ma_crossover_backtest.ipynb
```

**Kaggle**
1. Upload `ma_crossover_backtest.ipynb`
2. Settings → Internet → On
3. Run all

## Notes

- No lookahead bias — signals shifted 1 day before backtesting
- No test-fold leakage — parameter selection only ever sees train data
- Small parameter grids (9 SMA combos, 4 RSI thresholds) for runtime; could be widened
- 4 folds is enough to check consistency, not enough for strong statistical power
