There is some standard boilerplate code to make seaborn run in a notebook.

```python
import pandas as pd
pd.plotting.register_matplotlib_converters()
import matplotlib.pyplot as plt
%matplotlib inline
import seaborn as sns
print("Setup Complete")
```

Import the dataset using `pd.read_csv([filepath])`, optionally setting the `index_col`.

To make a very simple plot based on the index, run

```python
plt.figure(figsize=([width],[height]))
sns.lineplot(data=[dataframe])
```