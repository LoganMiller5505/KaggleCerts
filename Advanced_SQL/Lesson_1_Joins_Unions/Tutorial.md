For this example, there are two imaginary tables: `owners` and `pets`.

The `PET_ID` column in the `owners` table corresponds to the `ID` column in the `pets` table.

---
**INNER JOIN** pulls rows from both tables where the value in `Pet_ID` has a match in the `ID` column of the pets table.

In this case, owners without pets are not included in the results. 

To create a table containing all rows from the owners table, use a **LEFT JOIN**. ("Left" refers to the table that appears before the **JOIN**, "Right" refers to the table after the **JOIN**)

Similarly, if a **RIGHT JOIN** gets all rows from the pets table.

Finally, a **FULL JOIN** returns all rows from both tables, regardless of NULL entries.

---
**JOIN**s combine results "horizontally", while **UNION**s combine results vertically, concactenating them.

With a **UNION**, the data types must be the same, but column names can be different.

**UNION ALL** includes duplicate values. To drop duplicate values, use **UNION DISTINCT** instead.

---
Example: Pull information from the `stories` and `comments` tables to create a table showing all stories posted on January 1, 2012, along with the corresponding number of comments. (Use a **LEFT JOIN** so that the results include stories that didn't receive any comments).
```sql
WITH c AS
(
SELECT parent, COUNT(*) as num_comments
FROM `bigquery-public-data.hacker_news.comments`
GROUP BY parent
)
SELECT s.id as story_id, s.by, s.title, c.num_comments
FROM `bigquery-public-data.hacker_news.stories` AS s
LEFT JOIN c
ON s.id = c.parent
WHERE EXTRACT(DATE FROM s.time_ts) = '2012-01-01'
ORDER BY c.num_comments DESC
```
To select all usernames corresponding to users who wrote stories or comments on January 1, 2014. (Use **UNION DISTINCT** instead of **UNION ALL** to ensure that each user appears in the table at most once)
```sql
SELECT c.by
FROM `bigquery-public-data.hacker_news.comments` AS c
WHERE EXTRACT(DATE FROM c.time_ts) = '2014-01-01'
UNION DISTINCT
SELECT s.by
FROM `bigquery-public-data.hacker_news.stories` AS s
WHERE EXTRACT(DATE FROM s.time_ts) = '2014-01-01'
```