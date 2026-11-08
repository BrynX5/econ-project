# Forecasting U.S. Unemployment from Labor Market Indicators

Can last quarter's labor market and macro indicators forecast next quarter's unemployment rate better than assuming it stays the same?

## Approach

- **Data:** 9 public FRED series, averaged to quarters from 2001 onward: unemployment, JOLTS job openings, CPI, real GDP growth, labor force participation, average hourly earnings, the 10-year minus 2-year Treasury spread, initial jobless claims, and personal consumption.
- **A true forecast:** every indicator is measured one quarter *before* the quarter being predicted. The model predicts the *change* in unemployment, so it never has to extrapolate to levels it hasn't seen.
- **Honest testing:** a walk-forward test over the last 20% of quarters. Each quarter, the model is retrained on only the data available before it, then forecasts that one quarter.
- **Baselines:** a random forest is compared with "no change" (next quarter equals this quarter) and a linear ridge regression. All errors are mean absolute error in percentage points.

## Results

See the final cells of [`labor_market_forecast.ipynb`](labor_market_forecast.ipynb) for the error table, the forecast chart and feature importance.

## Run it

Open the notebook in Google Colab, or locally run:

```
pip install -r requirements.txt
jupyter notebook labor_market_forecast.ipynb
```

No API key is needed: the data comes from FRED's public CSV downloads.

## Limits

- About 100 quarters in total and about 20 tested, so small differences are noise.
- The 2020 COVID spike is in the training data and can distort what the models learn.
- FRED serves today's revised data, so real-time accuracy would be somewhat worse.
- Feature importance shows what the model relies on, not cause and effect.

Data: [Federal Reserve Bank of St. Louis, FRED](https://fred.stlouisfed.org/).
