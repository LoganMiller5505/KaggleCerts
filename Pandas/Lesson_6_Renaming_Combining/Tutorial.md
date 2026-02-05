`rename()` lets you change index/column names
```python
reviews.rename(columns={'points':'score'})
```
To rename just some elements (example using the index):
```python
reviews.rename(index={0:'firstEntry'}, 1:'secondEntry')
```
---
Generally, renaming columns is more common than renaming indexes, where `set_index()` is more convenient.

`rename_axis()` changes the `name` attribute for both the row and column index
```python
reviews.rename_axis("wines", axis='rows').rename_axis("fields", axis='columns')
```
---
The three operations for combining different DataFrames/Series are `concat()`, `join()`, and `merge()`.

---
The simplest is `concat()`, which will combine elements along an axis. This is useful when data in different DataFrames/Series have the same columns.
```python
canadian_youtube = pd.read_csv("../input/youtube-new/CAvideos.csv")
british_youtube = pd.read_csv("../input/youtube-new/GBvideos.csv")

pd.concat([canadian_youtube, british_youtube])
```
---
The next most complex is `join()`. This combines DataFrames which have a common index.
```python
left = canadian_youtube.set_index(['title', 'trending_date'])
right = british_youtube.set_index(['title', 'trending_date'])

left.join(right, lsuffix='_CAN', rsuffix='_UK')
```
The `lsuffix` and `rsuffix` parameters are necessary because the data has the same column names in both datasets. Without this, they wouldn't be necessary.