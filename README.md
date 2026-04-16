# Next-Day Stock Return Prediction: AAPL

This was a capstone project for my data science programme. The goal was to predict AAPL's next-day adjusted-close return using not just the stock's own history but also market-level (QQQ) and sector-level context.

Honestly, predicting stock returns is hard and anyone who tells you their model is reliably profitable is probably overfitting. The point of this project was the methodology, specifically, building a proper time-aware pipeline that doesn't leak future data into training, which is embarrassingly easy to get wrong with financial data.

---

## Problem

Given data up to day *t*, predict the return on day *t+1* for AAPL.  
Then translate that predicted return into a predicted price.

---

## Data

[Stock Market Dataset by jacksoncrow on Kaggle](https://www.kaggle.com/datasets/jacksoncrow/stock-market-dataset)

- AAPL as the target stock
- QQQ as market proxy
- MSFT, GOOGL, AMZN, META, NVDA as peer-based tech sector index (equal-weighted)
- Period: 2018 train / 2019 validation / Jan–Mar 2020 test

---

## Features

- Lagged returns (1, 2, 3, 5 days)
- Rolling moving averages (SMA5, SMA20) and gap from current price
- Volatility measures (rolling std over 5 and 20 days)
- Market lagged returns and volatility
- Sector index lagged returns and volatility
- High-low spread normalised by 20-day z-score
- Calendar effects (day of week, month)

---

## Models

| Model | Notes |
|-------|-------|
| Elastic Net | Linear baseline with L1+L2 regularisation |
| Random Forest | Shallow trees to avoid overfitting |
| Gradient Boosting | Best individual performer |
| Stacked Ensemble | Lasso meta-learner on OOF predictions |
| Weighted Ensemble | Inverse-MAE weighted average |

Time-series cross-validation (TimeSeriesSplit) throughout; no shuffling, no lookahead.

---

## Ablation Study

Ran a feature ablation on validation set comparing:
- Own stock features only
- Own + market features
- Own + market + sector features

Sector features added marginal improvement for the linear model, less so for trees.

---

## Results

The models beat the naive baseline on MAE and RMSE. Sign accuracy is around 52–55% depending on the model, roughly what you'd expect for a task this noisy.

The test period (Jan–Mar 2020) includes the COVID crash, which made evaluation interesting. The models obviously didn't predict that, no model trained on 2018–2019 data would.

---

## Tools

Python, pandas, numpy, scikit-learn, matplotlib

---

*Capstone project for IIT Guwahati x Masai School Credit-Linked Data Science Programme.*

