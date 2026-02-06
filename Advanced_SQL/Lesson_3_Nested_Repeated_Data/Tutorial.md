Suppose there is a dataset with a `pets` table and a `toys` table. The `toys` table could contain a "Pet_ID" column that matches each toy to the pet that owns it.

An alternate option is to organize the information in a single table "`pets_and_toys`".

Here, all information from the `toys` table is collapsed into a single column "Toy". This column is a **nested** column, with the "Name" and "Type" fields nested inside of it.

These nested columns have type **STRUCT** (or type **RECORD**).

To query a column with nested data, identify each field in the context of the column that contains it:
- `Toy.Name` refers to the "Name" field in the "Toy" column
- `Toy.Type` refers to the "Type" field in the "Toy" column

---
Now consider the case where each pet can have multiple toys. To collapse this into one table, we need to contain **repeated data**. This has type **REPEATED**.

Each entry is an **ARRAY**. When querying this data, put the name of the coumn containing the repeated data inside an **UNNEST()** function.

This flattens the repeated data so that there is one element on each row.

---
A column can be both nested and repeated.

First, you need to **UNNEST()** the column since it is repeated, and the individual fields can be referred to like a typical nested column.

---
Ex: Counting the number of transactions an hour for the Google Merchandise store
```sql
SELECT device.browser AS device_browser,
       SUM(totals.transactions) as total_transactions
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_20170801`
GROUP BY device_browser
ORDER BY total_transactions DESC
```
By storing the information in "device" and "totals" columns as STRUCTs, expensive JOINs are avoided. This increases performance and eliminates the need to worry about JOIN keys and which exact tables are needed.

---
Ex: Query most popular landing point on the website
```sql
SELECT hits.page.pagePath as path,
       COUNT(hits.page.pagePath) as counts
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_20170801`,
     UNNEST(hits) as hits
WHERE hits.type="PAGE" and hits.hitNumber=1
GROUP BY path
ORDER BY counts DESC
```