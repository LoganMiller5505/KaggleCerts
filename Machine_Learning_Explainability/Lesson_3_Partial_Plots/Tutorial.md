Partial dependence plots show *how* a feature affects predictions. Like permutation importance, they are calculated after a model has been fit.

For a single observation, repeated predictions are made while changing just one variable. The predicted outcomes are traced out. That is repeated for multiple rows from the original dataset, and the average predicted outcome is shown on the vertical axis.

---
Here is how to create a Partial Dependence Plot on a simple model:
```python
from matplotlib import pyplot as plt
from sklearn.inspection import PartialDependenceDisplay

disp1 = PartialDependenceDisplay.from_estimator(tree_model, val_X, ['Goal Scored'])
plt.show()
```

---
To show interactions between features, 2D partial dependence plots are also useful. Example:
```python
fig, ax = plt.subplots(figsize=(8, 6))
f_names = [('Goal Scored', 'Distance Covered (Kms)')]

disp4 = PartialDependenceDisplay.from_estimator(tree_model, val_X, f_names, ax=ax)
plt.show()
```
These graphs show predictions for any combination of the two features