`book.title.describe()` gives a high-level summary of that given column. Changes based on the datatype

`book.points.mean()` returns an average

`book.title.unique()` generates a list of all unique values

`book.title.counts()` generates a list of unique values AND how often they occur in the dataset

---
map() takes in a function that expects a single value from the Series and returns a transformed version of that value. map() then returns a new Series where all the values have been transformed by the function

Example: Remeans the scores to 0
```
review_points_mean = review.points.mean()
reviews.points.map(lambda p: p - review_points_mean)
```
---
apply() is an equivalent method for transforming an entire DataFrame by calling the custom method on each row
```
def remean_points(row):
    row.points = row.points - review_points_mean
    return row

reviews.apply(remean_points, axis='columns')
```
If calling `reviews.apply()` with `axis='index'`, then give a function to transform each column, not each row

---
`map()` and `apply()` return new, transformed Series and DataFrames, respectively. They do not modify the original data they're on

---
All standard Python operators and other ones such as concatenation and subtraction work as operators between equal length Series, and these will be faster than maps but less flexible when applying conditional logic