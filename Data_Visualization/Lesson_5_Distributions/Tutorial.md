To create a histogram, specify what column you'd like to create one for. Example:
```python
sns.histplot(iris_data['Petal Length (cm)'])
```

To create a KDE plot, specify what column you'd like to create one for and whether you would like it to be shaded or not. Example:
```python
sns.kdeplot(data=iris_data['Petal Length (cm)'], shade=True)
```
You can also create a 2D KDE plot by joining two plots. Example:
```python
sns.jointplot(x=iris_data['Petal Length (cm)'], y=iris_data['Sepal Width (cm)'], kind="kde")
```

You can create histograms with different colors and specifying the hue to set the column which splits the data. Example:
```python
sns.histplot(data=iris_data, x='Petal Length (cm)', hue='Species')
plt.title("Histogram of Petal Lengths, by Species")
```

You can do this for KDE plots using the same `kdeplot` function used above. Example:
```python
sns.kdeplot(data=iris_data, x='Petal Length (cm)', hue='Species', shade=True)
plt.title("Distribution of Petal Lengths, by Species")
```