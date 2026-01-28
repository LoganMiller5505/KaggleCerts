COUNT() will return the number of entries in a column when passed the name of a column

Example: Return how many IDs are in the table
```sql
SELECT COUNT(ID)
FROM `bigquery-public-data.pet_records.pets `
```
---
COUNT() is an *aggregate function* (takes many values and returns one). Other aggregate functions include:
- SUM()
- AVG()
- MIN()
- MAX()
---
GROUP BY takes the name of one or more columns, and treats all rows with the same value as one group when applying aggregate functions (such as COUNT())

Example: Know how many of each type of animal are in the pets table
```sql
SELECT Animal, COUNT(ID)
FROM `bigquery-public-data.pet_records.pets`
GROUP BY Animal
```
---
HAVING is used in combination with GROUP BY to ignore groups that don't meet certain criteria

Example: Only include groups that have more than one ID
```sql
SELECT Animal, COUNT(ID)
FROM `bigquery-public-data.pet_records.pets`
GROUP BY Animal
HAVING COUNT(ID) > 1
```
---
Example: Find which comments on a forum generate the most replies
- Parent column indicates the comment that was replied to
- ID column has the unique ID for each comment

Therefore, we can GROUP BY the parent column and COUNT() the id column to figure out how mnay comments were made as responses to a specific comment
```sql
SELECT parent, COUNT(id)
FROM `bigquery-public-data.hacker_news.full`
GROUP BY parent
HAVING COUNT(id) > 10
```