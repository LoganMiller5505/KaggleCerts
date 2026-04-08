MAE (Mean Absolute Error) is a way to summarize model quality. It averages the error (actual-predicted) for all elements. To calculate MAE for an sklearn trained model:
```python
from sklearn.metrics import mean_absolute_error

predicted_home_prices = melbourne_model.predict(X)
mean_absolute_error(y, predicted_home_prices)
```

---
A common mistake is making predictions with training data and comparing those predictions to the target values in training data. This creates an "in sample" score.

It is important to create a train test split to avoid this. This can be done in scikit by:
```python
from sklearn.model_selection import train_test_split

train_X, val_X, train_y, val_y = train_test_split(X, y, random_state = 0)
```
The "train" features are used during training and the "val" features are used during validation.