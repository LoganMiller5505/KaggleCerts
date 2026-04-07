Scatterplots, similar to barplots, need to specify an x and y axis. Example:
```python
sns.scatterplot(x=insurance_data['bmi'], y=insurance_data['charges'])
```
You can add a regression line to this using `sns.regplot` in place of `sns.scatterplot`.

You can make a color-coded scatterplot to show three relationships by adding a `hue` specification. Example:
```python
sns.scatterplot(x=insurance_data['bmi'], y=insurance_data['charges'], hue=insurance_data['smoker'])
```
Multiple regression lines can be added using `sns.lmplot` instead of `sns.scatterplot`. This has a slightly different format, as seen in this example:
```python
sns.lmplot(x="bmi", y="charges", hue="smoker", data=insurance_data)
```

There is also a swarmplot, which features a categorical variable on one of the axes. Example:
```python
sns.swarmplot(x=insurance_data['smoker'],
              y=insurance_data['charges'])
```