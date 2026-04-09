SHAP values break down a single prediction to show the impact of each feature. These can later be aggregated into powerful model-level insights.

They are calculated by using some "baseline value" which is then compared to the actual value used in that prediction. This prediction is repeated for all other features.

---
The Shap library is used to calculated SHAP values, as shown here:
```python
import shap

explainer = shap.TreeExplainer(my_model)

shap_values = explainer.shap_values(data_for_prediction)

shap.initjs()
shap.force_plot(explainer.expected_value[1], shap_values[1], data_for_prediction)
```
There are different explainers for more than just Tree models, included `shap.DeepExplainer` and `shap.KernelExplainer` (which works with all models, though is slower and often an approximation rather than exact). An example of the Kernel Explainer in use:
```python
k_explainer = shap.KernelExplainer(my_model.predict_proba, train_X)
k_shap_values = k_explainer.shap_values(data_for_prediction)
shap.force_plot(k_explainer.expected_value[1], k_shap_values[1], data_for_prediction)
```