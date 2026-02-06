Even if a column has dates, that doesn't mean its a proper datetime object (would show up as just "object")

To convert a date column to datetime, use [strftime](https://strftime.org/) formatting.

For example, 1/17/07 has the format "%m/%d/%y", and 17-1-2007 has the format "%d-%m-%Y"

To parse a date such as 1/17/07, use
```python
landslides['date_parsed'] = pd.to_datetime(landslides['date'], format="%m/%d/%y")
```

Now, the dtype in the dataframe will be in the datetime64 object type.

---
To extract information (like day of the month)
```python
day_of_month_landslides = landslides['date_parsed'].dt.day
```
---
It's important not to mix up days and months. Other problems can be returned in an error message, but this can be problematic.