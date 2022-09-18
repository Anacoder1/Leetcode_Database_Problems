```
Table: Purchases

+---------------+------+
| Column Name   | Type |
+---------------+------+
| purchase_id   | int  |
| user_id       | int  |
| purchase_date | date |
+---------------+------+

purchase_id is the primary key for this table.

This table contains logs of the dates that users purchased from a certain retailer.

 
Write a SQL query to report the IDs of the users that made any two purchases at most 7 days apart.

Return the result table ordered by user_id.

The query result format is in the following example.

 
Example 1:

Input: 
Purchases table:
+-------------+---------+---------------+
| purchase_id | user_id | purchase_date |
+-------------+---------+---------------+
| 4           | 2       | 2022-03-13    |
| 1           | 5       | 2022-02-11    |
| 3           | 7       | 2022-06-19    |
| 6           | 2       | 2022-03-20    |
| 5           | 7       | 2022-06-19    |
| 2           | 2       | 2022-06-08    |
+-------------+---------+---------------+

Output: 
+---------+
| user_id |
+---------+
| 2       |
| 7       |
+---------+

Explanation: 
User 2 had two purchases on 2022-03-13 and 2022-03-20. Since the second purchase is within 7 days of the first purchase, we add their ID.
User 5 had only 1 purchase.
User 7 had two purchases on the same day so we add their ID.
```
<br>

> The first solution is taken from [here](https://github.com/kamyu104/LeetCode-Solutions/blob/master/MySQL/users-with-two-purchases-within-seven-days.sql)
<br>

```SQL
CREATE TABLE Purchases (purchase_id int, user_id int, purchase_date date, PRIMARY KEY (purchase_id));

INSERT INTO Purchases VALUES
(4, 2, '2022-03-13'), (1, 5, '2022-02-11'),
(3, 7, '2022-06-19'), (6, 2, '2022-03-20'),
(5, 7, '2022-06-19'), (2, 2, '2022-06-08');


WITH purchase_cte AS (
    SELECT user_id, purchase_date,
           LAG(purchase_date) OVER (PARTITION BY user_id ORDER BY purchase_date) AS prev_purchase_date
    FROM Purchases
)
SELECT DISTINCT user_id
FROM purchase_cte
WHERE DATEDIFF(purchase_date, prev_purchase_date) <= 7
ORDER BY user_id ASC;
```
***