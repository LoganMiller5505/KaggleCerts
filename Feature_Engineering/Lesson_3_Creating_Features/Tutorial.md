Tips on Discovering New Features
- Understand the features. Refer to data documentation if possible
- Research the problem domain to acquire **domain knowledge**
- Study previous work
- Use visualization to reveal pathologies in the distribution of a feature or complicated relationship.

---
**Mathematical Transforms** can be used directly on pandas columns.

Example of simple division:
```python
autos["stroke_ratio"] = autos.stroke / autos.bore
```

Example of complex formula:
```python
autos["displacement"] = (
    np.pi * ((0.5 * autos.bore) ** 2) * autos.stroke * autos.num_of_cylinders
)
```

Data visualization can help suggest these transformations, "reshaping: a feature through powers or log.

Example of using log to normalize:
```python
accidents["LogWindSpeed"] = accidents.WindSpeed.apply(np.log1p)

fig, axs = plt.subplots(1, 2, figsize=(8, 4))
sns.kdeplot(accidents.WindSpeed, shade=True, ax=axs[0])
sns.kdeplot(accidents.LogWindSpeed, shade=True, ax=axs[1]);
```

---
**Counts** can be used to describe the presence or absence of something which comes in sets. They can be made by adding up binary/boolean columns.

Example:
```python
roadway_features = ["Amenity", "Bump", "Crossing", "GiveWay",
    "Junction", "NoExit", "Railway", "Roundabout", "Station", "Stop",
    "TrafficCalming", "TrafficSignal"]
accidents["RoadwayFeatures"] = accidents[roadway_features].sum(axis=1)
```

Similarly, you can create your own boolean values using the greater than method:
```python
components = [ "Cement", "BlastFurnaceSlag", "FlyAsh", "Water",
               "Superplasticizer", "CoarseAggregate", "FineAggregate"]
concrete["Components"] = concrete[components].gt(0).sum(axis=1)
```

---
You can often "**build up**" or "**break down**" features such as IDs, phone numbers, addresses, etc. since they have some kind of underlying structure.

Example by using the string .split method:
```python
customer[["Type", "Level"]] = (  # Create two new features
    customer["Policy"]           # from the Policy feature
    .str                         # through the string accessor
    .split(" ", expand=True)     # by splitting on " "
                                 # and expanding the result into separate columns
)
```

Example of joining simple features into a combined feature:
```python
autos["make_and_style"] = autos["make"] + "_" + autos["body_style"]
```

---
**Group Transforms** aggregate information across multiple rows, grouped by some category. Essentially, this "combines" a categorical variable with some values.

Examples:
```python
customer["AverageIncome"] = (
    customer.groupby("State")  # for each state
    ["Income"]                 # select the income
    .transform("mean")         # and compute its mean
)

customer["StateFreq"] = (
    customer.groupby("State")
    ["State"]
    .transform("count")
    / customer.State.count()
)
```

Example of using a transform to create a "frequency encoding":
```python
df_train = customer.sample(frac=0.5)
df_valid = customer.drop(df_train.index)

df_train["AverageClaim"] = df_train.groupby("Coverage")["ClaimAmount"].transform("mean")

df_valid = df_valid.merge(
    df_train[["Coverage", "AverageClaim"]].drop_duplicates(),
    on="Coverage",
    how="left",
)
```
Note that, if using training and validation splits, it's best to create a grouped feature using only the training set and then join it to the validation set using `merge` after creating a unique set of values with `drop_duplicates`.

---
Tips on Creating Features
- Linear models learn sums and differences naturally, but can't learn anything more complex
- Ratios are difficult for most models to learn, so ratio combinations often lead to performance gain
- Linear models and neural nets generally do better with normalized features. Tree-based models can sometimes benefit from normalization, but usually less so
- Tree models can learn to approximate almost any combination of features, but especially important combinations can still benefit from being explicitly created
- Counts are especially helpful for tree models, since they can't naturally aggregate information across many features at once