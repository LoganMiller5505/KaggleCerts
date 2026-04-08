Data leakage happens when training data contains information about the target that will not be available when the model is used for prediction. This is one reason the model can perform well on the training set, but poorly in production.

---
**Target leakage** occurs when predictors include data that will not be available at the time you make predictions. Think about target leakeage in terms of the *timing* or *order* that data becomes available.

Example: Predicting who will get sick with pneumonia, and using "took antibiotic medicine" as a feature. People take this *after* getting pneumonia, potentially leaking that result even when, if used for actual prediction, people would not yet have taken antibiotics.

---
**Train-Test Contamination**
Mixing what should be separate training and validation data. A simple example is running preprocessing (such as an imputer) before calling `train_test_split()`. This means that data from validation was used to fit the preprocessing.

This can be avoided by making sure to exclude the validation data from any type of fitting, including preprocessing steps. This is much easier to do when using scikit-learn pipelines, especially with cross-validation.