SHAP summary plots give a birds-eye view of feature importance and what drives it, as opposed to permutation importance which only shows which features mattered (but not how).

Example of creating a SHAP summary plot:
```python
import shap

explainer = shap.TreeExplainer(my_model)

shap_values = explainer.shap_values(val_X)

shap.summary_plot(shap_values[1], val_X)
```
Note that calculating SHAP values can be slow with a larger dataset (with the exception of `xgboost` model).

---
To delve into a single feature, the SHAP dependence contribution plots can help. These can be interpreted to show interaction between different elements.

Example of creating a SHAP dependence contribution plot:
```python
import shap

explainer = shap.TreeExplainer(my_model)

shap_values = explainer.shap_values(X)

shap.dependence_plot('Ball Possession %', shap_values[1], X, interaction_index="Goal Scored")
```