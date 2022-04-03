```
Table: Customers

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| customer_id   | int     |
| customer_name | varchar |
+---------------+---------+

customer_id is the primary key for this table.

Each row of this table contains the name and the id customer.


Write an SQL query to find the missing customer IDs.

The missing IDs are ones that are not in the Customers table but are in the range between 1 and the maximum customer_id present in the table.

Notice that the maximum customer_id will not exceed 100.

Return the result table ordered by ids in ascending order.

The query result format is in the following example.

Customer table:
+-------------+---------------+
| customer_id | customer_name |
+-------------+---------------+
| 1           | Alice         |
| 4           | Bob           |
| 5           | Charlie       |
+-------------+---------------+

Result table:
+-----+
| ids |
+-----+
| 2   |
| 3   |
+-----+

The maximum customer_id present in the table is 5, so in the range [1,5], IDs 2 and 3 are missing from the table.
```
<br>

> Refer [this link](https://leetcode.ca/2020-04-30-1613-Find-the-Missing-IDs/) for an example of how CTEs work.
<br>

```SQL
CREATE TABLE Customers (customer_id int, customer_name varchar(32));

INSERT INTO Customers VALUES
(1, 'Alice'), (4, 'Bob'), (5, 'Charlie');


WITH RECURSIVE CTE AS (
    SELECT 1 AS 'id', MAX(C.customer_id) AS 'max_id'
    FROM Customers C
    UNION ALL
    SELECT id + 1, max_id
    FROM CTE
    WHERE id < max_id
)
SELECT id AS 'ids'
FROM CTE C
WHERE C.id NOT IN (SELECT customer_id FROM Customers)
ORDER BY 1 ASC;


/* Generate all numbers from 1 to 100, then select the numbers in the range 1 to 100 not in customer_id */

SELECT ids
FROM (
    SELECT X * 10 + Y + 1 AS ids
    FROM (
        SELECT 0 AS X UNION SELECT 1 UNION SELECT 2 UNION SELECT 3 UNION SELECT 4 UNION SELECT 5 UNION SELECT 6 UNION SELECT 7 UNION SELECT 8 UNION SELECT 9 
    ) AS tens,
    (
        SELECT 0 AS Y UNION SELECT 1 UNION SELECT 2 UNION SELECT 3 UNION SELECT 4 UNION SELECT 5 UNION SELECT 6 UNION SELECT 7 UNION SELECT 8 UNION SELECT 9
    ) AS ones
    ORDER BY ids
) AS num
WHERE ids < (SELECT MAX(customer_id) FROM Customers)
AND ids NOT IN (SELECT DISTINCT customer_id FROM Customers);


WITH RECURSIVE CTE (ids) AS (
    SELECT 1 AS ids
    UNION ALL
    SELECT ids + 1
    FROM CTE
    WHERE ids < (SELECT MAX(customer_id) FROM Customers)
)
SELECT ids
FROM CTE
WHERE ids NOT IN (SELECT customer_id FROM Customers)
ORDER BY ids;
```
***