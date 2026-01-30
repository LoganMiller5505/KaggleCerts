If the data is spread across multiple tables, JOIN becomes incredibly important

---
Example:

"pets" table has three columns
- ID - ID number for the pet
- Name - Name of the pet
- Animal - Type of animal

"owners" table has three columns
- ID - ID number for the owner
- Name - name of the owner
- Pet_ID - ID number for the pet that belongs to the owner

We need to match based on ID in pets and Pet_ID in owners

Using JOIN, you can create a table with just two columns: the name of the pet and the name of the owner
```sql
SELECT p.Name as Pet_Name, o.Name AS Owner_name
FROM `bigquery-public-data.pet_records.pets` AS p
INNER JOIN `bigquery-public-data.pet_records.owners` AS o ON p.ID = o.Pet_ID
```
Combine information from both tables by matching rows where ID in pets matches Pet_ID in owners

ON determines which column is used to combine the tables. Since ID exists in both tables, p.ID refers to ID in pets, and o.Pet_ID refers to the Pet_ID column from the owners table

It's generally a good idea to specify which table each column comes from

This join is an INNER JOIN. That means a row will only be put in the final output table if the value in the combined columns shows up in both tables

---
Example: How many files are covered by each type of software license?
- licenses table, which provides the name of each GitHub repo (in the repo_name column) and its corresponding license
- sample_files table, which provides the GitHub repo that each file belongs to (in the repo_name column)
```sql
SELECT L.license, COUNT(1) AS numer_of_files
FROM `bigquery-public-data.github_repos.files` AS sf
INNER JOIN `bigquery-public-data.github_repos.licenses` AS L
ON sf.repo_name = L.repo_name
GROUP BY L.license
ORDER BY number_of_files DESC
```
Breaking this down:
```sql
`bigquery-public-data.github_repos.files` AS sf
INNER JOIN `bigquery-public-data.github_repos.licenses` AS L
ON sf.repo_name = L.repo_name
```
This specifies the sources of data and how to join them. ON specifies to combine the tables by matching the values in the repo_name columns in the tables
```sql
SELECT L.license, COUNT(1) AS numer_of_files
FROM
...
GROUP BY L.license
```
GROUP BY breaks the data into a different group for each license, before COUNTing the number of rows in the sample_files table corresponding to each license
```sql
ORDER BY number_of_files DESC
```
Sorts the results so that licenses with more files appear first

---
LIKE tag can be used to further filter strings
```sql
SELECT id, title, owner_user_id
FROM `bigquery-public-data.stackoverflow.posts_questions`
WHERE tags LIKE '%bigquery%'
```
The % acts as a "wildcard" for any amount of characters