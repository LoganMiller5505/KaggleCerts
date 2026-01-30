Need to start with
```python
import pandas as pd
```
---
DataFrame is a table. It has an array of entries, each of which has a value. Each entry corresponds to a row (or record) and a column
```python
pd.DataFrame({'Yes': [50, 21], 'No': [131, 2]})
```

DataFrames are not limited to integers
```python
pd.DataFrame({'Bob': ['I liked it.', 'It was awful.'], 'Sue': ['Pretty good.', 'Bland.']})
```

The `pd.DataFrame()` constructor includes a dictionary whose keys are column names, and whose values are a list of entries

By default, it uses row labels ascending from 0. You can asign a custom index using the index parameter in the constructor
```python
pd.DataFrame({'Bob': ['I liked it.', 'It was awful.'], 
              'Sue': ['Pretty good.', 'Bland.']},
             index=['Product A', 'Product B'])
```
---
A series is a sequence of values, comparable to a list
```python
pd.Series([1, 2, 3, 4, 5])
```
A series is essentially a single column of a DataFrame (or a DataFrame is just many Series "glued together"), but does not have a column name, only an overall name

---
It is possible to load a DataFrame from a .csv file using
```
df = pd.read_csv("../input/path/to/data/data.csv")
```
You also can optionally specify many parameters, including what column to use as an index
```
df = pd.read_csv("../input/path/to/data/data.csv", index_col = 0)
```
---
You can view the shape of a DataFrame using
```
df.shape()
```
---
You can examine the first 5 rows of a DataFrame using
```
df.head()
```
---