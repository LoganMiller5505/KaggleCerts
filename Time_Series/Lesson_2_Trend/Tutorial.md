The **trend** component of a time series represents a powerful, long-term change in the mean of the series. It is the slowest-moving and represnts the largest time scale of importance.

---
To see the kind of trend a time series might have, we can use a **moving average plot**. To compute the moving average of a time series, compute the average of the values within a sliding window of some defined width. This helps smooth out any short-term fluctuations in the series so that only long-term changes remain.

Once the shape of the trend is identified, we can attempt to model it using a time-step feature. You can modify the linear time dummy feature to fit other kinds of trends. For example, to fit a quadratic trend, add the square of the time dummy to the feature set, giving `target = a * time ** 2 + b * time + c`.

If you can provide curves of the appropriate shape as features, linear regression can learn how to combine them in a way that best fits the target.

---
This example will create a trend model for a tunnel traffic dataset.

First, let's make a moving average plot with a window of 365 days to smooth over short-term annual changes. To create a moving average, use the `rolling` method to begin a window computation followed by the `mean` method to compute the average over the window:
```python
moving_average = tunnel.rolling(
    window=365,
    center=True,
    min_periods=183,
).mean()

ax = tunnel.plot(style=".", color="0.5")
moving_average.plot(
    ax=ax, linewidth=3, title="Tunnel Traffic - 365-Day Moving Average", legend=False,
);
```

Rather than engineering the time dummy in Pandas like before, we'll instead use a function from the `statsmodel` library called the `DeterministicProcess`. This will help us avoid some tricky failure cases that can arise with time series and linear regression. `Order` refers to polynomial order:
```python
from statsmodels.tsa.deterministic import DeterministicProcess

dp = DeterministicProcess(
    index=tunnel.index,
    constant=True,
    order=1,
    drop=True,
)
X = dp.in_sample()

X.head()
```

Create the trend model similarly to before, but adding `fit_intercept=False`:
```python
from sklearn.linear_model import LinearRegression

y = tunnel["NumVehicles"]

model = LinearRegression(fit_intercept=False)
model.fit(X, y)

y_pred = pd.Series(model.predict(X), index=X.index)
```

To make a forecast, apply the model to "out of sample" features. Here's an example of a thirty day forecast:
```python
X = dp.out_of_sample(steps=30)

y_fore = pd.Series(model.predict(X), index=X.index)

y_fore.head()
```

---
The trend models can be useful as a baseline for more sophisticated model and can be used in a "hybrid model" with algorithms unable to learn trends.