The first step with a new dataset is to take a look at it, especially to search for missing values (represented by `NaN` or `None`)
```python
nfl_data.head()
```
---
Next is to see how many missing values are in each column
```python
missing_values_count = nfl_data.isnull().sum()
```
These can also be expressed as a percentage
```python
total_cells = np.product(nfl_data.shape)
total_missing = missing_values_count.sum()

precent_missing = (total_missing/total_cells)*100
```
---
At this point, it's time to intuit on the data and figure out why the missing values exist, or: **Is this value missing because it wasn't recorded or because it doesn't exist?**

If a value doesn't esit, then it may be best to keep these as NaN.

If a value wasn't recorded, than you should impute it.

Reading the dataset documentation can also greatly assist at this step

---
One option is to drop rows with missing values
```python
nfl_data.dropna()
```
This can remove all data. It is often better to just remove columns that have a missing value.
```python
nfl_data.dropna(axis=1)
```

---
Another option is to fill in missing values.

One option is `fillna()`
```python
nfl_data.fillna(0)
```

Another option is to replace the missing values with whateber value comes directly after it in the same column, which makes sense for datasets with a logical order.
```python
nfl_data.fillna(method='bfill', axis=0).fillna(0)
```
