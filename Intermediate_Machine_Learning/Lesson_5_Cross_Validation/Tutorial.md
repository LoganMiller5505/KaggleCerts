Cross-validation runs the modeling process on different subsets of the data to get multiple measures of model quality. It does this in "folds", where each fold has an equal percentage but different subset of the data in validation as opposed to training.

While it gives a more accurate measure of quality, it takes longer to run. This means it is much more efficient for small datasets than larger datasets.

---
It is possible to do cross-validation without pipelines, but it is much harder. The `cross_val_score()` provides an easy way to to this, as shown here:
```python
from sklearn.model_selection import cross_val_score

scores = -1 * cross_val_score(my_pipeline, X, y,
                              cv=5,
                              scoring='neg_mean_absolute_error')

print("MAE scores:\n", scores)

print("Average MAE score (across experiments):")
print(scores.mean())
```