There are two things to establish before designing a forecast model:
- What information is available at the time a forecast is made (features)
- The time period during which you require forecasted values (target)

The **forecast origin** is the time at which you make a forecast. This can also be though of as the last time for which you have training data for the time being predicted.

The **forecast horizon** is the time for which you are making a forecast.

The time between the origin and the horizon is the **lead time**. It may be possible for a forecast to begin multiple steps ahead of the origin because of delays in data acquisition or processing.

---
To forecast time series with ML algorithms, we need to transform the series into a usable dataframe. Each row in a dataframe represents a single forecast. The time index is the first time in the forecast horizon, but we arrange values for the entire horizon in the same row. For multistep forecasts, this means we are requiring a model to produce multiple outputs, one for each step.

---
There are a number of strategies for producing the multiple target steps required for a forecast. Here are four common strategies with their strengths and weaknesses:
1. Multioutput model

    Use a model that produces multiple outputs naturally. Linear regression and neural networks can produce multiple outputs. This is simple and efficient, but not possible for every algorithm (for example, XGBoost)

2. Direct strategy

    Train a separate model for each step in the horizon: one model forecasts 1-step, another forests 2-steps, etc. The downside is that training lots of models can be computationally expensive.

3. Recursive strategy

    Train a single one-step model and use its forecasts to update the lag features for the next step. With the recursive method, we feed a model's 1-step forecast back in to the same model to use as a lag feature for the next forecasting step. We only need to train one model this way, but any errors will propagate from step to stop, so forecasts can be inaccurate for long horizons.

4. DirRec strategy

    A combination of direct and recursive: train a model for each step and use forecasts from previous steps as *new* lag features. Step by step, each model gets an additional lag input. Since each model always has an up-to-date set of lag features, the DirRec strategy can capture serial dependence better than Direct, but can also suffer from error propagation like Recursive.

---
Example: Applying MultiOutput and Direct strategies to *Flu Trends* data. We'll define the forecasting task to have an 8-week horizon with a 1-week lead time.

First, prepare the target series for multistep forecasting:
```python
def make_lags(ts, lags, lead_time=1):
return pd.concat(
    {
        f'y_lag_{i}': ts.shift(i)
        for i in range(lead_time, lags + lead_time)
    },
    axis=1)

y = flu_trends.FluVisits.copy()
X = make_lags(y, lags=4).fillna(0.0)

def make_multistep_target(ts, steps):
return pd.concat(
    {f'y_step_{i + 1}': ts.shift(-i)
        for i in range(steps)},
    axis=1)

y = make_multistep_target(y, steps=8).dropna()

y, X = y.align(X, join='inner', axis=0)
```
From here, training and prediction is the same for a multioutput model.

XGBoost, however, can't produce multiple outputs for regression tasks. But using the Direct reduction strategy, it can still produce multi-step forecasts. This can be done by wrapping it with scikitlearn's `MultiOutputRegressor`:
```python
from sklearn.multioutput import MultiOutputRegressor

model = MultiOutputRegressor(XGBRegressor())
model.fit(X_train, y_train)

y_fit = pd.DataFrame(model.predict(X_train), index=X_train.index, columns=y.columns)
y_pred = pd.DataFrame(model.predict(X_test), index=X_test.index, columns=y.columns)
```

For DirRec, you would just replace `MultiOutputRegressor` with `RegressorChain`. The recursive strategy would need to be coded manually.