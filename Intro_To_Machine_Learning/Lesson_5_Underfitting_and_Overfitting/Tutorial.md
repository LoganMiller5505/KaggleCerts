**Overfitting** is where a model matches the training data, but performs poorly in validation or with other new data.

**Underfitting** is where a model does not even fit well to the training data.

It is important to find the "sweet spot" between these two extremes.

---
With decision trees, controlling the tree depth is one way to prevent overfitting. The `DecisionTreeRegressor` has a `max_leaf_nodes` argument which does this exact thing.

To check different candidate values for this, the following code sample works well:
```python
from sklearn.metrics import mean_absolute_error
from sklearn.tree import DecisionTreeRegressor

def get_mae(max_leaf_nodes, train_X, val_X, train_y, val_y):
    model = DecisionTreeRegressor(max_leaf_nodes=max_leaf_nodes, random_state=0)
    model.fit(train_X, train_y)
    preds_val = model.predict(val_X)
    mae = mean_absolute_error(val_y, preds_val)
    return(mae)

for max_leaf_nodes in [5, 50, 500, 5000]:
    my_mae = get_mae(max_leaf_nodes, train_X, val_X, train_y, val_y)
    print("Max leaf nodes: %d  \t\t Mean Absolute Error:  %d" %(max_leaf_nodes, my_mae))
```