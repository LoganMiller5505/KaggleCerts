Gradient boosting goes through cycles to iteratively add models into an ensemble. It intiailizes the ensemble with a single model with naive predictions. Then, it starts a cycle:
1. Generate predictions for each observation in the dataset.
2. These predictions calculate a loss function.
3. The loss function fits a new model that will be added to the ensemble. 
4. The new model gets added; repeat.

---
`xgbost` is its own library with an `XBRegressor` class. Example of importing and evaluating it:
```python
from xgboost import XGBRegressor

my_model = XGBRegressor()
my_model.fit(X_train, y_train)

from sklearn.metrics import mean_absolute_error

predictions = my_model.predict(X_valid)
print("Mean Absolute Error: " + str(mean_absolute_error(predictions, y_valid)))
```

The parameters of this model are:
- n_estimators

    Specifies how many times to go through the modeling cycle. Equal to the number of models included in the ensemble. Too *low* = *underfitting*, too *high* = *overfitting*. Example:
    ```python
    my_model = XGBRegressor(n_estimators=500)
    my_model.fit(X_train, y_train)
    ```

- early_stopping_rounds

    Helps find the ideal `n_estimators` value by stopping iteration when the model has not improved for a number of iterations equal to the `early_stopping_rounds` value.

    It is common to set a high `n_estimators` and set `early_estimators` to a lower value, perhaps around 5.

    Note that use of `early_stopping_rounds` relies on passing in an eval set of X_train and y_train. Example:
    ```python
    my_model = XGBRegressor(n_estimators=500)
    my_model.fit(X_train, y_train, 
                early_stopping_rounds=5, 
                eval_set=[(X_valid, y_valid)],
                verbose=False)
    ```

- learning_rate

    Instead of just adding up the predictions from each model in the ensemble, the predictions can be multiplied by a small number before adding them in. Effectively, this means each new tree added helps less, allowing a higher value for `n_estimators` without overfitting. Example:
    ```python
    my_model = XGBRegressor(n_estimators=1000, learning_rate=0.05)
    my_model.fit(X_train, y_train, 
                early_stopping_rounds=5, 
                eval_set=[(X_valid, y_valid)], 
                verbose=False)
    ```

- n_jobs

    On larger datasets, parallelism can be used to build models faster. This usually is set to the number of cores on the machine.
    ```python
    my_model = XGBRegressor(n_estimators=1000, learning_rate=0.05, n_jobs=4)
    my_model.fit(X_train, y_train, 
                early_stopping_rounds=5, 
                eval_set=[(X_valid, y_valid)], 
                verbose=False)
    ```