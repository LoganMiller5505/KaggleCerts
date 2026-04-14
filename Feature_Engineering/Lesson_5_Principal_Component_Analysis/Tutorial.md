PCA is another method for feature engineering. Just as clustering partitions the dataset based on proximity, PCA can be seen as partitioning based on variation. Instead of describing the data with the original features, we describe it with its axes of variation, which become the new features.

The new features PCA constructs are really just linear combinations of the original features. Example:
```python
df["Size"] = 0.707 * X["Height"] + 0.707 * X["Diameter"]
df["Shape"] = 0.707 * X["Height"] - 0.707 * X["Diameter"]
```

These are the **principal components** of the data. The weights themselves are called **loadings**. There will be as many principal components as there are features in the original dataset.

---
There are two ways to use PCA for feature engineering.

The first way is as a descriptive technique. Since the components tell you about the variation, you can compute the MI scores for them and see what variation is most predictive of the target. That could give ideas for kinds of features to create.

The second way is to use the components themselves as features. Some use-cases:
- **Dimensionality Reduction**: With redundant features, PCA will partition out redundancy into one or more near-zero variance components which can then be dropped
- **Anomaly Detection**: Unusual variation which is not apparent from original features can show up in low-variance components. They could be highly informative in anomaly/outlier detection tasks
- **Noise reduction**: A collection of sensor readings will often share some common background noise. PCA can collect the signal into a smaller number of features while leaving the noise alone, boosting the signal-to-noise ratio.
- **Decorrelation**: Since some ML algorithms struggle with highly-correlated features, PCA can transform these into uncorrelated components which could be easier to work with.

Essentially, PCA gives direct access to the correlational structure of the data.

---
In this example, PCA will be used as a descriptive technique to discover features.

Here are four features that cover a range of properites, which each have a high MI score with the target. They will be standardized since they aren't naturally on the same scale:
```python
features = ["highway_mpg", "engine_size", "horsepower", "curb_weight"]

X = df.copy()
y = X.pop('price')
X = X.loc[:, features]

X_scaled = (X - X.mean(axis=0)) / X.std(axis=0)
```
Then scikit-learn's PCA estimator can be fit to create the principal components:
```python
from sklearn.decomposition import PCA

pca = PCA()
X_pca = pca.fit_transform(X_scaled)

component_names = [f"PC{i+1}" for i in range(X_pca.shape[1])]
X_pca = pd.DataFrame(X_pca, columns=component_names)
```
After fitting, the PCA instance contains the loadings in its `components_` attributes. We'll wrap these up in a dataframe.
```python
loadings = pd.DataFrame(
    pca.components_.T,  # transpose the matrix of loadings
    columns=component_names,  # so the columns are the principal components
    index=X.columns,  # and the rows are the original features
)
```
Remember that the signs and magnitudes of a component's loadings reveal what kind of variation it's captured. You can use a figure to show how the four chosen features vary.
```python
plot_variance(pca)
```
Components which have a significant relationship with the target can be examined to find worthwhile relationships.
```python
mi_scores = make_mi_scores(X_pca, y, discrete_features=False)
```
```python
idx = X_pca["PC3"].sort_values(ascending=False).index
cols = ["make", "body_style", "horsepower", "curb_weight"]
df.loc[idx, cols]
```
This informs that a new ratio feature may be suited
```python
df["sports_or_wagon"] = X.curb_weight / X.horsepower
sns.regplot(x="sports_or_wagon", y='price', data=df, order=2);
``