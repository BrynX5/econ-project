# Forecasting U.S. Unemployment from Labor Market Indicators

Can last quarter's labor market and macro indicators forecast next quarter's unemployment rate better than assuming it stays the same?

**Short answer: no.** Over 16 test quarters (2022 Q4 to 2026 Q3), a random forest missed by 0.13 percentage points on average, 53% worse than simply predicting no change (0.085 points). A linear ridge model roughly tied the no-change baseline (0.083 points). Over a one-quarter horizon, unemployment is persistent enough that these indicators add little beyond its current level.

## Approach

- **Data:** 9 public FRED series, averaged to quarters. The sample starts in 2006, when the average hourly earnings series begins: unemployment, JOLTS job openings, CPI, real GDP growth, labor force participation, average hourly earnings, the 10-year minus 2-year Treasury spread, initial jobless claims, and personal consumption.
- **A true forecast:** every indicator is measured one quarter *before* the quarter being predicted. The model predicts the *change* in unemployment, so it never has to extrapolate to levels it hasn't seen.
- **Honest testing:** a walk-forward test over the last 20% of quarters. Each quarter, the model is retrained on only the data available before it, then forecasts that one quarter.
- **Baselines:** a random forest is compared with "no change" (next quarter equals this quarter) and a linear ridge regression. All errors are mean absolute error in percentage points.

## Results

| Model | Mean absolute error (pct points) | vs. no-change baseline |
|---|---|---|
| Linear ridge | 0.083 | −2% |
| No change (baseline) | 0.085 | — |
| Random forest | 0.131 | +53% |

Walk-forward test over 16 quarters (2022 Q4 to 2026 Q3); 78 usable quarters from 2007 Q2. Full output: [`results/metrics.json`](results/metrics.json) and the notebook.

![One-quarter-ahead forecasts and feature importance](figures/forecast_and_importance.png)

**What the chart shows**
- The random forest tracks the direction of unemployment but overshoots, most visibly in late 2024 and mid-2026. Each overshoot costs more than the no-change baseline's small, steady lag.
- The forest relies mostly on last quarter's GDP growth (48% of importance) and the year-over-year change in jobless claims (27%). Both swung furthest during the 2008 recession and the 2020 COVID shock, so the model likely learned those extremes and overreacts to ordinary moves.
- A 2% edge for ridge over 16 quarters is within noise, so the fair reading is a tie with the baseline, not a win.

## Run it

Open the notebook in Google Colab, or locally run:

```
pip install -r requirements.txt
jupyter notebook labor_market_forecast.ipynb
```

No API key is needed: the data comes from FRED's public CSV downloads.

## Limits

- Only 78 quarters in total and 16 tested, so small differences are noise.
- The 2020 COVID spike is in the training data and can distort what the models learn.
- FRED serves today's revised data, so real-time accuracy would be somewhat worse.
- Feature importance shows what the model relies on, not cause and effect.

Data: [Federal Reserve Bank of St. Louis, FRED](https://fred.stlouisfed.org/).
