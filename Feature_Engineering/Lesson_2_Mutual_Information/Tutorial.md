A general first step is to construct a ranking with a **feature utility metric** which measures associations between a feature and the target. The metric to be used first is "mutual information", which measures a relationship between two quantities. It is powerful because it can detect *any* kind of relationship, while correlation only detects *linear* relationships.

It describes relationships in terms of *uncertainty*. It is a measure of the extent to which knowledge of one quantity reduces uncertainty about the other.

When MI is zero, the quantities are independent. Higher MI values demonstrate a stronger relationship.

Some things to consider:
- MI can help you understand the *relative potential* of a feature considered by itself.
- It is possible for a feature to be very informative combined with other features, but not informative by itself. MI cannot detect this, it is a **univariate** metric.
- The *actual* usefulness of a feature *depends on the model*. Just because it has a high MI score doesn't mean the model will be able to do anything with the information; it may need to be transformed first.

---
Example of computing and plotting MI scores:
```python
from sklearn.feature_selection import mutual_info_regression

def make_mi_scores(X, y, discrete_features):
    mi_scores = mutual_info_regression(X, y, discrete_features=discrete_features)
    mi_scores = pd.Series(mi_scores, name="MI Scores", index=X.columns)
    mi_scores = mi_scores.sort_values(ascending=False)
    return mi_scores

mi_scores = make_mi_scores(X, y, discrete_features)
print(mi_scores[::3])

def plot_mi_scores(scores):
    scores = scores.sort_values(ascending=True)
    width = np.arange(len(scores))
    ticks = list(scores.index)
    plt.barh(width, scores)
    plt.yticks(width, ticks)
    plt.title("Mutual Information Scores")


plt.figure(dpi=100, figsize=(8, 5))
plot_mi_scores(mi_scores)
```
You may need to generate additional plots to uncover the actual relationships even if a feature is seemingly unimportant. Example:
```python
sns.relplot(x="curb_weight", y="price", data=df);
sns.lmplot(x="horsepower", y="price", hue="fuel_type", data=df);
```