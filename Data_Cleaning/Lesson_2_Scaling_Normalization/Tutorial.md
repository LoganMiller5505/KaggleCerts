Scaling: Changes the *RANGE* of the data

Normalization: Changes the *shape of the distribution* of the data

---
Scaling transforms the data to fit within a specific scale (e.g. 0-100 or 0-1).

The shape of the data doesn't change, but the range does

```python
scaled_data = minmax_scaling(original_data, columns=[0])
```

---
Normalization changes the observations so that they can be described as a normal distribution.

This is mostly used for machine learning or statistics techniques that require normalization as an assumption.

```python
normalized_data = stats.boxcox(original_data)
```