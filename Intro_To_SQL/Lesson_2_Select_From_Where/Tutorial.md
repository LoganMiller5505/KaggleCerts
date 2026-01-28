To select a single column from a single table:
- Specify the column you want after **SELECT**
- Specify the table after the word **FROM**

Example: Select Name column from pets table in pet_records database in bigquery-public-data project

```sql
SELECT Name
FROM `bigquery-public-data.pet_records.pets`
```
---
To return rows meeting specific conditions, use the **WHERE** clause

Example: Return entries from the Name column that are in rows where the Animal column has the text 'Cat'
```sql
SELECT Name
FROM `bigquery-public-data.pet_records.pets`
WHERE Animal = 'Cat'
```

Example: Return all items from "city" column where "country" column is 'US' in the opanaq dataset for global air quality.
```sql
SELECT city
FROM `bigquery-public-data.openaq.global_air_quality`
WHERE country = 'US'
```

---

To submit a query to the databse with python bigquery, you first create a client object just as before
```python
client = bigquery.client()
```
Then, use the query() method to setup a query
```python
query_job = client.query(query)
```
Next, run the query and convert the results to a pandas DF
```python
us_cities = query_job.to_dataFrame()
```
You can use this just how you would any other dataframe! Ex:
```python
us_cities.city.value_counts().head()
```
---
You can query multiple columns using a comma
```sql
SELECT city, country
FROM `bigquery-public-data.openaq.global_air_quality`
WHERE country = 'US'
```
---
You can select all columns with a *
```sql
SELECT *
FROM `bigquery-public-data.openaq.global_air_quality`
WHERE country = 'US'
```
---
To estimate the size of a query before running it, create a QueryJobConfig object and set the dry_run parameter to True
```python
query = """
        SELECT score, title
        FROM `bigquery-public-data.hacker_news.full`
        WHERE type = "job" 
        """

dry_run_config = bigquery.QueryJobConfig(dry_run=True)

dry_run_query_job = client.query(query, job_config=dry_run_config)

print("This query will process {} bytes.".format(dry_run_query_job.total_bytes_processed))
```
You can also specify a parameter when running the query to limit how much data you are willing to scan.
```python
ONE_MB = 1000*1000
safe_config = bigquery.QueryJobConfig(maximum_bytes_billed=0 NE_MB)

safe_query_job = client.query(query, job_config=safe_config)

safe_query_job.to_dataframe()
```
This way, the query will error out if it exceeds 1 MB.