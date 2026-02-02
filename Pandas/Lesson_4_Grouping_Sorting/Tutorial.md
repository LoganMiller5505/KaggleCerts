groupby() groups data and then does something specific to the entire group the data is in.

You can replicate `value_count()` using
```
reviews.groupby('points').points.count()
```
In this example, `groupby()` created a group of reviews which grouped wines by points values and counted how many were in each group. 

This works with any summary function, for example, to get the cheapest wine in each point category, use
```
reviews.groupby('points').price.min()
```
---
Each group can be seen as a "slice" of the DataFrame containing only matching values. This DataFrame is accessible using the `apply()` method, and can then be manipulated.

Example: Selecting the name of the first wine reviewed from each winery
```
reviews.groupby('winery').apply(lambda df: df.title.iloc[0])
```

You can even group by more than one column

Example: Picking the best wine by country and province
```
reviews.groupby(['country','province']).apple(lambda df: df.loc[df.points.idmax()])
```
---
agg() lets you run several different functions on the DataFrame simultaneously

Example: Generate a simple statistical summary
```
reviews.groupby(['country']).price.egg([len, min, max])
```
---
groupby() can, depending on the operation run, will sometimes result in a multi-index. It has multiple levels (compared to a single index).

In general, the most frequently used multi-index method is `reset_index()`, which resets it back to single index.

---
Grouping returns data in index order, not value order. The `sort_values()` method is good for adjusting this. It defaults to an ascending sort, but for a descending sort, pass in the parameter `ascending=False`.

To sort by index values, use `sort_index()`. You can also sort by more than one column at a time, using `sort_values(by=['column1','column2'])`