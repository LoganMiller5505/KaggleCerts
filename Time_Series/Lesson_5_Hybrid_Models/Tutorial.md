Linear regression excels at extrapolating trends, but can't learn interactions. XGBoost excels at learning interactions, but can't extrapolate trends. This is the motivation behind creating "hybrid" forecasters that combine complementary learning algorithms and let the strengths of one make up for the weakness of the other.

---
Many time series can be closely described by an additive model of trend, seasons, and cycles plus unpredictable, random error.

Each of the terms is a **component** of the time series.

The **residuals** are the difference between the target and the predictions. Plotting the residuals against a feature gives the "left over" part of the target, or what the model failed to learn about the target from that feature.

---
It's possible to use one algorithm for some of the components and another algorithm for the rest. This is done to choose the best algorithm for each component. This is done in the following process:
```python
# 1. Train and predict with first model
model_1.fit(X_train_1, y_train)
y_pred_1 = model_1.predict(X_train)

# 2. Train and predict with second model on residuals
model_2.fit(X_train_2, y_train - y_pred_1)
y_pred_2 = model_2.predict(X_train_2)

# 3. Add to get overall predictions
y_pred = y_pred_1 + y_pred_2
```

---
There are two ways a regression algorithm can make predictions: either by transforming the *features* or by transforming the *target*. 

Feature-transforming algorithms learn some mathematical function that takes features as an input and then combines and transforms them to produce an output that matches the target values in the training set. Examples are linear regression and neural nets.

Target-transforming algorithms use the features to group the target values in the training set and make predictions by averaging values in a group; a set of feature just indicates which group to average. Examples are decision trees and nearest neighbors.

The important thing is that feature transformers generally can **extrapolate** target values beyond the training set given appropriate features as inputs, but the predictions of target transformers will always be bound within the range of the training set.

This is what motivates hybrid design in this lesson: use linear regression to extrapolate the trend, transform the *target* to remove the trend, and apply XGBoost to the detrended residuals. To hybridize a neural net (feature transformer) you could instead include the predictions of another model as a feature, which the neural net would then include as part of its own predictions. The method of fitting to residuals is the same method the gradient boosting algorithm uses, so these will be called **boosted** hybrids: the method of using predictions as features is known as "stacking", so we will call these **stacked** hybrids.

---
Example: Projecting US Retail Sales. First, let's use a linear regression model to learn the trend. Then, to predict multiple series at once with XGBoost, convert the series from *wide* format to *long* format:
```python
X = retail.stack()
display(X.head())
y = X.pop('Sales')
```

In order for XGBoost to distinguish the two time series, `Industries` will become a categorical feature with a label encoding:
```python
X = X.reset_index('Industries')
for colname in X.select_dtypes(["object", "category"]):
    X[colname], _ = X[colname].factorize()

X["Month"] = X.index.month 

X_train, X_test = X.loc[idx_train, :], X.loc[idx_test, :]
y_train, y_test = y.loc[idx_train], y.loc[idx_test]
```

Now convert the trend predictions made earlier to long format and subtract them from the original series. That will give residual series that XGBoost can learn:
```python
y_fit = y_fit.stack().squeeze()
y_pred = y_pred.stack().squeeze()

y_resid = y_train - y_fit

xgb = XGBRegressor()
xgb.fit(X_train, y_resid)

y_fit_boosted = xgb.predict(X_train) + y_fit
y_pred_boosted = xgb.predict(X_test) + y_pred
```