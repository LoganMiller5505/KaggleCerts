The data type for a column in a DataFrame or a Series is known as the dtype.
```python
reviews.price.dtype
```
The dtypes property can also return the dtype of every column in the dataframe
```python
reviews.dtype
```
---
Note: Strings are given the generic "object" type

---
It's possible to convert a column of one type into another using the `astype()` function
```python
reviews.points.astype('float64')
```
---
Indexes have their own dtype, as well
```python
reviews.index.dtype
```
---
Missing values are given an `NaN` value, which are of the float64 dtype

To select NaN entires, use `pd.isnull()` (or the alternative `pd.notnull()`)
```python
reviews[pd.isnull(reviews.country)]
```
---
Replace missing values with `fillna()` which replaces all NaN values with the given value
```python
reviews.region_2.fillna("Unknown")
```
---
You can also use `replace()` to replace non-null values
```python
reviews.taster_twitter_handle.replace("@kerinokeefe", "@kerino")
```