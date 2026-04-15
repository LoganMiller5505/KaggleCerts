The basic object of forecasting is the **time series**, which is a set of observations recorded over time. They are typically recorded with a regular frequency (for example, daily or monthly).

---
The linear regression algorithm can be used to construct forecasting models. It learns how to make a weighted sum from its input features. For two features, the formula would be `target = weight_1 * feature_1 + weight_2 * feature_2 + bias`. During training, the algorithm would learn the values for the parameters `weight_1`, `weight_2`, and `bias` that best fit the `target`. The weights are also called *regression coefficients* and the `bias` is also called the *intercept*.

---
There are two kinds of features unique to time series: time-step features and lag features.
- Time-step features can be derived directly from the time index. The most basic is the **time dummy**, which counts off time steps from beginning to end. Linear regression with the time dummy produces the model `target = weight * time + bias`. These let you model **time dependense**.
- Lag features shift the observations of the target series so that they appear to have occured later in time. You can create a 1-step lag feature, or you can use multiple steps. Linear regression with a lag feature produces the model `target = weight * lag + bias`. These let you model **serial dependense**, or when an observation can be predicted from previous observations.

---
Applying machine learning algorithms to time series problems is largely about feature engineering with the time index and lags.

An example of this is below, which follows tunnel traffic data.

To create a time dummy:
```python
df = tunnel.copy()

df['Time'] = np.arange(len(tunnel.index))

df.head()
```

To fit a linear regression model:
```python
from sklearn.linear_model import LinearRegression

X = df.loc[:, ['Time']]
y = df.loc[:, 'NumVehicles']

model = LinearRegression()
model.fit(X, y)

y_pred = pd.Series(model.predict(X), index=X.index)
```

To create a lag feature, use the `shift` method in Pandas:
```python
df['Lag_1'] = df['NumVehicles'].shift(1)
df.head()
```

To handle missing values, you can fill them or "backfill" with the first known value. In this example, missing values are dropped (along with their corresponding target value):
```python
from sklearn.linear_model import LinearRegression

X = df.loc[:, ['Lag_1']]
X.dropna(inplace=True)
y = df.loc[:, 'NumVehicles']
y, X = y.align(X, join='inner')

model = LinearRegression()
model.fit(X, y)

y_pred = pd.Series(model.predict(X), index=X.index)
```

---
The best time series models will usually use some combination of time-step features and lag features.