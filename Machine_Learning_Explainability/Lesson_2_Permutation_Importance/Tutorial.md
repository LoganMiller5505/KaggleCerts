There are multiple ways to measure feature importance, one of which is **permutation importance**. It is fast to calculate, widely used and understand, and consistent with properties desirable for a feature importance measure to have.

It is calculated *after* a model is fitted. It essentially asks the question, "If I randomly shuffle a single column in the validation data, how does that affect the accuracy of predictions in the new-shuffled data?"

This way, models which rely heavily on a feature will be significantly affected by this suffering, while a feature that is less important will not be affected as much.

This gets repeated for all columns in the validation dataset.

---
For a rudimentary fitted model, calculate permutation importance with the following code snippet:

```python
import eli5
from eli5.sklearn import PermutationImportance

perm = PermutationImportance(my_model, random_state=1).fit(val_X, val_y)
eli5.show_weights(perm, feature_names = val_X.columns.tolist())
```

Features near the top matter more than features near the bottom. "Negative importance" is likely due to randomness combined with low importance.