Decision trees are lower performant than a lot of newer models. The **random forest** is an evolution of the decision tree. It uses many trees, and makes a prediction by averaging the result of each component tree. 

It is constructed similar to the `RandomForestRegressor`, using the `DecisionTreeRegressor` instead as shown below.

```python
from sklearn.ensemble import RandomForestRegressor
from sklearn.metrics import mean_absolute_error

forest_model = RandomForestRegressor(random_state=1)
forest_model.fit(train_X, train_y)
melb_preds = forest_model.predict(val_X)
print(mean_absolute_error(val_y, melb_preds))
```