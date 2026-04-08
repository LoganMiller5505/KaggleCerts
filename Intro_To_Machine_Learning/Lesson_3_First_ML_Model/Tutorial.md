Often, you will need to limit which columns you observe in the dataset. This is done using the **columns** property with `dataframe.columns`.

---
To select the prediction target, you can use **dot-notation** to access one Series of the DataFrame.

By convention, the prediction target is called **y**. Example: `y = data.Target`

---
To select features, use column names inside brackets to access several columns in the DataFrame.

By convention, this data is called **X**. Example:
```python
melbourne_features = ['Rooms', 'Bathroom', 'Landsize', 'Lattitude', 'Longtitude']
X = melbourne_data[melbourne_features]
```

---
**Scitkitlearn** will be used to build models. The steps to building an using a model are:
1. **Define**: What type of model will it be? Some other parameters of the model type are specified too.
2. **Fit**: Capture patterns from provided data.
3. **Predict**
4. **Evaluate**: Determine how accurate the prediction's are.

Here is an example of defning a decision tree and fitting it.
```python
from sklearn.tree import DecisionTreeRegressor

# Random_state ensures the same results each run
melbourne_model = DecisionTreeRegressor(random_state=1)

melbourne_model.fit(X, y)
```
And how to predict using the model.
```python
melbourne_model.predict(X.head())
```