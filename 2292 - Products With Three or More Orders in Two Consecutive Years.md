```
Table: Orders

+---------------+------+
| Column Name   | Type |
+---------------+------+
| order_id      | int  |
| product_id    | int  |
| quantity      | int  |
| purchase_date | date |
+---------------+------+

order_id is the primary key for this table.

Each row in this table contains the ID of an order, the id of the product purchased, the quantity, and the purchase date.


Write a SQL query to report the IDs of all the products that were ordered three or more times in two consecutive years.

Return the result table in any order.

The query result format is shown in the following example.

Example 1:

Input: 
Orders table:
+----------+------------+----------+---------------+
| order_id | product_id | quantity | purchase_date |
+----------+------------+----------+---------------+
| 1        | 1          | 7        | 2020-03-16    |
| 2        | 1          | 4        | 2020-12-02    |
| 3        | 1          | 7        | 2020-05-10    |
| 4        | 1          | 6        | 2021-12-23    |
| 5        | 1          | 5        | 2021-05-21    |
| 6        | 1          | 6        | 2021-10-11    |
| 7        | 2          | 6        | 2022-10-11    |
+----------+------------+----------+---------------+

Output: 
+------------+
| product_id |
+------------+
| 1          |
+------------+

Explanation: 
Product 1 was ordered in 2020 three times and in 2021 three times. Since it was ordered three times in two consecutive years, we include it in the answer.
Product 2 was ordered one time in 2022. We do not include it in the answer.
```
<br>

> The first solution is taken from [here](https://www.jiakaobo.com/leetcode/2292.%20Products%20With%20Three%20or%20More%20Orders%20in%20Two%20Consecutive%20Years.html)
<br>

> The second solution is taken from [here](https://icode.best/i/15469749423900)
<br>

> [DOES NOT WORK] The third solution is taken from [here](https://github.com/kamyu104/LeetCode-Solutions/blob/master/MySQL/products-with-three-or-more-orders-in-two-consecutive-years.sql)
<br>

```SQL
CREATE TABLE Orders (order_id int, product_id int, quantity int, purchase_date date, PRIMARY KEY (order_id));

INSERT INTO Orders VALUES
(1, 1, 7, '2020-03-16'), (2, 1, 4, '2020-12-02'),
(3, 1, 7, '2020-05-10'), (4, 1, 6, '2021-12-23'),
(5, 1, 5, '2021-05-21'), (6, 1, 6, '2021-10-11'),
(7, 2, 6, '2022-10-11');


WITH cte AS (
    SELECT product_id, year(purchase_date) AS purchase_year
    FROM Orders
    GROUP BY purchase_year, product_id
    HAVING COUNT(order_id) >= 3
)
SELECT DISTINCT C1.product_id
FROM cte C1 JOIN cte C2
ON C1.product_id = C2.product_id
AND C1.purchase_year = C2.purchase_year + 1;


WITH T1 AS (
    SELECT product_id, YEAR(purchase_date) years, COUNT(order_id) cnt
    FROM Orders
    GROUP BY product_id, YEAR(purchase_date)
)
SELECT DISTINCT product_id
FROM (
    SELECT product_id, years - ROW_NUMBER() OVER (PARTITION BY product_id ORDER BY years) yrn
    FROM T1
    WHERE cnt >= 3
) S1
GROUP BY product_id, yrn
HAVING COUNT(1) >= 2;


WITH consecutive_cte AS (
    SELECT product_id,
           YEAR(purchase_date) AS curr_year,
           LEAD(YEAR(purchase_date), 1) OVER (PARTITION BY product_id ORDER BY YEAR(purchase_date)) - YEAR(purchase_date) AS year_diff
    FROM Orders
    GROUP BY 1, 2, purchase_date
    HAVING COUNT(order_id) >= 3
    ORDER BY NULL
)
SELECT DISTINCT product_id
FROM consecutive_cte
WHERE year_diff = 1;
```
***