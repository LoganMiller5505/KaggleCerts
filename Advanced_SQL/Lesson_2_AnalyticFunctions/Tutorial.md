Analytic  functions allow performing complex calculations with relatively simple syntax

---
For this example, `id` identifies each runner, `date` holds the day of the training session, and `time` shows the time (in minutes) that the runner dedicated to training.

The following query calculates a moving average of the training times for each runner:
```sql
SELECT *,
AVG(time)OVER(
    PARTITION BY id
    ORDER BY date
    ROWS BETWEEN 1 PRECEDING AND CURRENT ROW) as avg_time
FROM `bigquery-public-data.runners.train_time`
)
```

All analytic functions have an **OVER** clause, which defines the set of rows used in each calculation. There are three optional parts to this:

1. The **PARTITION BY** clause, which divides the rows of the table into different groups. In this example, dividing by `id` so that the calculations are separated by runner.
2. The **ORDER BY** clause, which defines ordering within each partition. In this example, ordering by the `date` column.
3. The final clause (`ROWS BETWEEN 1 PRECEDING AND CURRENT ROW`) is a **window frame** clause. It identifies the set of rows used in each calculation, called a **window**.
---
There are many ways to write window frame clauses:

- `ROWS BETWEEN 1 PRECEDING AND CURRENT ROW` - the previous row and the current row
- `ROWS BETWEEN 3 PRECEDING AND 1 FOLLOWING` - the 3 previous rows, the current row, and the following row
- `ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING` - all rows in the partition

---
There are three types of analytic functions

1. Analytic aggregate functions: Take all of the values within the window as input and return a single value

- Ex: `MIN()` or `MAX()`, `AVG()` or `SUM()`, `COUNT()`

2. Analytic navigation functions: Assign a value based on the value in a (usually) different row than the current row

- Ex: `FIRST_VALUE()` or `LAST_VALUE()` to return the first/last value in the input. `LEAD()` or `LAG()` to return the value on a subsequent/preceding row

3. Analytic numbering functions: Assign integer values to each row based on the ordering

- Ex: `ROW_NUMBER()` returns the order in which rows appear in the input (starting with 1). `RANK()` all rows with the same value receive the same rank value, where the next row receives a rank value which increments with the number of rows with the previous rank value.
---
Example: Use an analytic function to calculate the cumulative number of trips for each date in 2015
```sql
WITH trips_by_day AS
(
SELECT DATE(start_date) AS trip_date,
       COUNT(*) as num_trips
FROM `bigquery-public-data.san_francisco.bikeshare_trips`
WHERE EXTRACT(YEAR FROM start_date) = 2015
GROUP BY trip_date
)
SELECT *,
    SUM(num_trips)
        OVER (
            ORDER BY trip_date
            ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
        ) AS cumulative_trips
    FROM trips_by_day
```
Breaking it down, this query uses a common table expression (CTE) to first calculate the daily number of trips. Then, **SUM()** is used as an aggregate function.
- Since there is no **PARTITION BY** clause, the entire table is treated as a single partition
- The **ORDER BY** clause orders the row by date, where earlier dates appear first
- By setting the **window frame** clause to `ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`, all rows up to and including the current date are used to calculate the (cumulative) sum.

Example: Track the stations where each bike began (in start_station_id) and ended (in end_station_id) the day on October 25, 2015
```sql
SELECT bike_number,
    TIME(start_date) AS trip_time,
    FIRST_VALUE(start_station_id)
        OVER (
             PARTITION BY bike_number
             ORDER BY start_date
             ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
             ) AS first_station_id,
    LAST_VALUE(end_station_id)
        OVER (
             PARTITION BY bike_number
             ORDER BY start_date
             ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
             ) AS last_station_id,
    start_station_id,
    end_station_id
FROM `bigquery-public-data.san_francisco.bikeshare_trips`
WHERE DATE(start_date) = '2015-10-25'
```
Breaking it down, this query uses both **FIRST_VALUE()** and **LAST_VALUE()** as analytic functions.
- The **PARTITION BY** clause breaks the data into partitions based on the `bike_number` column. This ensures the calculations are performed separately for each bike.
- The **ORDER BY** clause puts the rows within each partition in chronological order.
- Since the **window frame** clause is `ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING`, for each row, its entire partition is used to perform the calculation.