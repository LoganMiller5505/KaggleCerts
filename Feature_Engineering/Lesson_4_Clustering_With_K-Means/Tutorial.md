**Clustering** involves assigning data points to groups based on how similar the points are to each other.

It acts as a "binning" or "discretization" transform when applied to a single real-valued feature. On multiple features, it's like "multi-dimensional binning" (or vector quantization).

Note that the `Cluster` feature is categorical. It can be label or one-hot encoded.

The primary idea behind cluster labels is that the clusters will break up complicated relationships across features into simpler chunks so the model can learn the simpler chunks one-by-one instead of having to learn it all at once.

---
One common clustering algorithm that we'll use here is k-means, which is intuitive and easy to apply for feature engineering. It measures similarity using Euclidean distance. It creates clusters by placing a number of **centroids** where each point in the dataset is assigned to the cluster of whatever centroid it's closest to. The "k" in "k-means" is how many centroids (and therefore, clusters) it creates.

For scikit-learn's implementation, there are three parameters to focus on:
1. `n_clusters`, which starts by being randomly initialized. It then iterates assigning points and moving each centroid to minimize the distance to its point until the centroids stop moving, or
2. `max_iter` is reached, stopping the clustering early.
3. If the initial random position of the centroids ends in a poor clustering, the algorithm will repeat `n_init` times and return the clustering with the least total distance between each point and its cluster.

You may need to increase `max_iter` for a large number of clusters or `n_init` for a complex dataset, but typically `n_clusters` is the only parameter you will need to set. This is often best tuned like a hyperparameter (through cross-validation for example).

---
Example of using Latitude and Longitutde features clustered with Median Income:
```python
kmeans = KMeans(n_clusters=6)
X["Cluster"] = kmeans.fit_predict(X)
X["Cluster"] = X["Cluster"].astype("category")

sns.relplot(
    x="Longitude", y="Latitude", hue="Cluster", data=X, height=6,
);

X["MedHouseVal"] = df["MedHouseVal"]
sns.catplot(x="MedHouseVal", y="Cluster", data=X, kind="boxen", height=6);
```

---
It is often a good idea to rescale or normalize data since k-means clustering is sensitive to scale.