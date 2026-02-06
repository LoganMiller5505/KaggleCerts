Queries that run many times or for huge datasets should be made efficient. Most database systems have a query optimizer, but there are still helpful strategies

1. **Only select the columns you want**
    
    Try to avoid using SELECT * just because it is convenient, since it can also be very inefficient, especially if there are unneeded text fields

2. **Read less data**

    Avoiding extra aggregate function calls can help in reading less data as the query is run

3. **Avoid N:N JOINs**

    1:1 JOINs mean each row in each table has at most one match in the other table

    N:1 JOIN means each row in one table potentially matches many rows in the other table

    N:N JOIN is where a group of rows in one table can match a group of rows in the other table. This produces a table with many more rows than either original table