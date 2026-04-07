To create a barchart, you will need to specify the x and y axes. Example:

```python
plt.figure(figsize=(10,6))
plt.title("Average Arrival Delay for Spirit Airlines Flights, by Month")
sns.barplot(x=flight_data.index, y=flight_data['NK'])
plt.ylabel("Arrival delay (in minutes)")
```

To create a heatmap, you follow a similar process to the rest of the charts. Example:

```python
plt.figure(figsize=(14,7))
plt.title("Average Arrival Delay for Each Airline, by Month")
sns.heatmap(data=flight_data, annot=True)
plt.xlabel("Airline")
```

Note that annot=True ensures that the values from the cell appear on the chart.