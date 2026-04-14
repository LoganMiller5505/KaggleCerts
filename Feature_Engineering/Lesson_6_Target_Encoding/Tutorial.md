A **target encoding** is any kind of encoding that replace a feature's categories with some number derived from the target. A simple example is applying a group aggregation:
```python
autos["make_encoded"] = autos.groupby("make")["price"].transform("mean")

autos[["make", "price", "make_encoded"]].head(10)
```
This is sometimes called a **mean encoding**. Applied to a binary target, it's also called **bin counting**.

---
This does introduce some problems. First are *unknown categories*. They create a special risk of overfitting, which means they need to be trained on an independent "encoding" split. When joined with future splits, Pandas will fill in missing values, but this requires some kind of imputing.

Second are *rare categories*. When a category only occurs a few times, any statistics calculated are unlikely to be very accurate. Target encoding rare categories can make overfitting more likely.

---
A solution to these problems is to add **smoothing**. This blends the *in-category* average with the *overall* average. In pseudocode, `encoding = weight * in_category + (1-weight) * overall` where `weight` is a value between 0 and 1 calculated from the category frequency.

An easy way to determine the value for `weight` is using an **m-estimate**: `weight = n / (n + m)` where `n` is the total number of times the category occurs in the data. `m` determines the "smoothing factor", where larger values of `m` put more weight on the overall estimate.

When choosing a value for `m`, consider how noisy you expect the categories to be. If they are noisy, it could be better to choose a larger `m` value.

---
Target encoding is great for:
- **High-cardinality features**: A feature with a large number of categories can be difficult to encode: one-hot generates too many features, label encoding might not be appropriate. Target encoding derives numbers for the categories using the feature's relationship with the target.
- **Domain-motivated features**: You may already suspect that a categorical feature should be important even if it scored poorly with a feature metric. Target encoding can help reveal a feature's true informativeness.

---
In this example, a movie-rating dataset will be used.

Start by creating a 25% split to train the target encoder:
```python
X = df.copy()
y = X.pop('Rating')

X_encode = X.sample(frac=0.25)
y_encode = y[X_encode.index]
X_pretrain = X.drop(X_encode.index)
y_train = y[X_pretrain.index]
```
`category_encoders` in `scikit_learn_contrib` implements an m-estimate encoder, which will be used to encode the `Zipcode` feature:
```python
from category_encoders import MEstimateEncoder

encoder = MEstimateEncoder(cols=["Zipcode"], m=5.0)

encoder.fit(X_encode, y_encode)

X_train = encoder.transform(X_pretrain)
```
Then, compare the encoded values to the target to see how informative the encoding might be:
```python
plt.figure(dpi=90)
ax = sns.distplot(y, kde=False, norm_hist=True)
ax = sns.kdeplot(X_train.Zipcode, color='r', ax=ax)
ax.set_xlabel("Rating")
ax.legend(labels=['Zipcode', 'Rating']);
```