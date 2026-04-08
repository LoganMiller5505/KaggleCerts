There are three approaches to handling categorical variables.

1. **Drop Categorical Variables**

    This will only work well if the columns don't contain useful information.

2. **Ordinal Encoding**

    Assign a unique value to a different integer. This usually assumes an ordering of the categories (those that have this are known as **ordinal variables**).

3. **One-Hot Encoding**

    Creates new columns indicating the presence or absence of each possible value in the original data.

    This does not assume an ordering of the categories, but does not work if there is no clear ordering in the categorical data.

---
You can get a list of categorical variables in this way:
```python
s = (X_train.dtypes == 'object')
object_cols = list(s[s].index)

print("Categorical variables:")
print(object_cols)
```

---
Example of dropping categorical variables:
```python
drop_X_train = X_train.select_dtypes(exclude=['object'])
drop_X_valid = X_valid.select_dtypes(exclude=['object'])
```

Example of ordinal encoding:
```python
from sklearn.preprocessing import OrdinalEncoder

label_X_train = X_train.copy()
label_X_valid = X_valid.copy()

ordinal_encoder = OrdinalEncoder()
label_X_train[object_cols] = ordinal_encoder.fit_transform(X_train[object_cols])
label_X_valid[object_cols] = ordinal_encoder.transform(X_valid[object_cols])
```

Example of one hot encoding:
```python
from sklearn.preprocessing import OneHotEncoder

OH_encoder = OneHotEncoder(handle_unknown='ignore', sparse=False)
OH_cols_train = pd.DataFrame(OH_encoder.fit_transform(X_train[object_cols]))
OH_cols_valid = pd.DataFrame(OH_encoder.transform(X_valid[object_cols]))

OH_cols_train.index = X_train.index
OH_cols_valid.index = X_valid.index

num_X_train = X_train.drop(object_cols, axis=1)
num_X_valid = X_valid.drop(object_cols, axis=1)

OH_X_train = pd.concat([num_X_train, OH_cols_train], axis=1)
OH_X_valid = pd.concat([num_X_valid, OH_cols_valid], axis=1)

OH_X_train.columns = OH_X_train.columns.astype(str)
OH_X_valid.columns = OH_X_valid.columns.astype(str)
```
Note, `handle_unknown='igore'` avoids errors when the validation data contains classes that aren't represented in the training data, and `sparse=False` ensures that the encoded columns are returned as a numpy array (instead of a sparse matrix).