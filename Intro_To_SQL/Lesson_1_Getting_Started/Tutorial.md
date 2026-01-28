This course uses the bigquery Python package
```python
from google.cloud import bigquery
```
---
1. Create a client object
```python
client = bigquery.Client()
```
2. Each dataset is stored in a corresponding "project".
To access it, construct a reference to the dataset using the client's dataset method
```python
dataset_ref = client.dataset("hacker_news", project="bigquery-public-data")
```
3. Then use the get_dataset() method with the reference to fetch the dataset
```python
dataset = client.get_dataset(dataset_ref)
```
---
list_tables() is used to list all tables in the dataset
```python
tables = list(client.list_tables(dataset))
```
And to print these:
```python
for table in tables:  
    print(table.table_id)
```
---
Similar to fetching a dataset, can use the table method to fetch an individual table instead
```python
table_ref = dataset_ref.table("full")
table = client.get_table(table_ref)
```
---
Structure of a table is its schema. View it by using:
```python
table.schema
```
Reveals
- Column names
- Dataypes
- Mode of column ("nullable" means NULL values are allowed)
- Description of data
---
Preview rows using the list_rows() method. This returns an iterator, so convert it to a Pandas dataframe using .to_dataframe()
```python
client.list_rows(table, max_results=5).to_dataframe()
```
Can also use this to look at specific columns
```python
client.list_rows(table, selected_fields=table.schema[:1], max_results=5).to_dataframe()
```