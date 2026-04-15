A time series exhibits **seasonality** when there is a regular, periodice change in the mean of the series. They generally follow the clock and calendar, and are often driven by cycles of the natural world by conventions of social behavior surrounding dates and times.

There are two kinds of features that model seasonality. The first kind, indicators, are best for a season with a few observations. The second kind, Fourier features, are best for a season with many observations, like an annual season of daily observations.

---
Just like using a moving average plot to discover the trend in a series, we can use a **seasonal plot** to discover seasonal patterns.

A seasonal plot shows segments of the time series plotted against some common period (where the period is the "season" you want to observe). 

**Seasonal indicators** are binary features that represent seasonal differences in the evel of a time series. They are what happens if you treat a seasonal period as categorical and apply one-hot encoding. Adding seasonal indicators to the training data helps models distinguish means within a seasonal period. The indicators act as On/Off switches.

---
Instead of creating a feature for each date, Fourier features try to capture the overall shape of the seasonal curve with just a few features. **Fourier features** are pairs of sine and cosine curves, one pair for each potential frequency in the season starting with the longest. If we add a set of these curves to the training data, the linear regression algorithm will figure out the weights that fit the seasonal component in the target series. By only needing the "main effect" of the seasonality with Fourier features, you'll usually need to add far fewer features to the training data, which means reduced computation time and less risk of overfitting.

---
The periodogram helps answer how many fourier pairs should be included in the feature set.

---
Let's look at seasonal plots over a week and over a year for tunnel traffic:
```python
X = tunnel.copy()

X["day"] = X.index.dayofweek 
X["week"] = X.index.week

X["dayofyear"] = X.index.dayofyear
X["year"] = X.index.year
fig, (ax0, ax1) = plt.subplots(2, 1, figsize=(11, 6))
seasonal_plot(X, y="NumVehicles", period="week", freq="day", ax=ax0)
seasonal_plot(X, y="NumVehicles", period="year", freq="dayofyear", ax=ax1);
```
To look at the periodogram:
```python
plot_periodogram(tunnel.NumVehicles);
```
Creating seasonal features using `DeterministicProcess`:
```python
from statsmodels.tsa.deterministic import CalendarFourier, DeterministicProcess

fourier = CalendarFourier(freq="A", order=10)  # 10 sin/cos pairs for "A"nnual seasonality

dp = DeterministicProcess(
    index=tunnel.index,
    constant=True,
    order=1,
    seasonal=True,
    additional_terms=[fourier],
    drop=True,
)

X = dp.in_sample()
```