One common way to check inconsitent data entry is by getting all unique values and sorting them alphabetically
```python
countries = professors['Country'].unique()
countries.sort()
```

For common errors, make everything lowercase and remove trailing white spaces
```python
professors['Country'] = professors['Country'].str.lower()
professors['Country'] = professors['Country'].str.strip()
```

---
For more advanced errors, the fuzzywuzzy package can help identify which strings are closest to each other using fuzzy matching. It returns a ratio given two strings which, the closer it is to 100, the smaller the edit distance between the strings.
```python
matches = fuzzywuzzy.process.extract("south korea", countries, limit=10, scorer=fuzzywuzzy.fuzz.token_sort_ratio)
```

You can then use these matches to replace them with the desired value