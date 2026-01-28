ORDER BY is usually the last clause and sorts the returned results

Example: Order rows by ID column
```sql
SELECT ID, Name, Animal
FROM `bigquery-public-data.pet_records.pets`
ORDER BY ID
```
Example: Order rows by Animal column (sorts alphabetically)
```sql
SELECT ID, Name, Animal
FROM `bigquery-public-data.pet_records.pets`
ORDER BY Animal
```
You can reverse the order using the DESC argument
```sql
SELECT ID, Name, Animal
FROM `bigquery-public-data.pet_records.pets`
ORDER BY Animal DESC
```
---
Dates can be sorted as either DATE or DATETIME
- DATE: YYYY-[M]M-[D]D
- DATETIME is the same, but with time added at the end

To look at one part of the date, use EXTRACT
```sql
SELECT Name, EXTRACT(DAY from Date) AS DAY
FROM `bigquery-public-data.pet_records_with_date`
```
Can even extract additional information, such as week (stored as 1-53 weeks in the year)
```sql
SELECT Name, EXTRACT(WEEK from Date) AS Week
FROM `bigquery-public-data.pet_records_with_date`
```
---
Example: Which day of the week has the most fatal motor accidents?
- consecutive_number column contains a unique ID for each accient
- timestamp_of_crash contains the date in DATETIME format
```sql
SELECT COUNT(consecutive_number) AS num_accidents, 
       EXTRACT(DAYOFWEEK FROM timestamp_of_crash) AS day_of_week
FROM `bigquery-public-data.nhtsa_traffic_fatalities.accident_2015`
GROUP BY day_of_week
ORDER BY num_accidents DESC
```