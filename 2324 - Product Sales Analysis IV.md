```
Table: Sales

+-------------+-------+
| Column Name | Type  |
+-------------+-------+
| sale_id     | int   |
| product_id  | int   |
| user_id     | int   |
| quantity    | int   |
+-------------+-------+

sale_id is the primary key of this table.

product_id is a foreign key to Product table.

Each row of this table shows the ID of the product and the quantity purchased by a user.


Table: Product

+-------------+------+
| Column Name | Type |
+-------------+------+
| product_id  | int  |
| price       | int  |
+-------------+------+

product_id is the primary key of this table.

Each row of this table indicates the price of each product.


Write a SQL query that reports for each user the product id on which the user spent the most money.

In case the same user spent the most money on two or more products, report all of them.

Return the resulting table in any order.

The query result format is in the following example.

Example 1:

Input: 
Sales table:
+---------+------------+---------+----------+
| sale_id | product_id | user_id | quantity |
+---------+------------+---------+----------+
| 1       | 1          | 101     | 10       |
| 2       | 3          | 101     | 7        |
| 3       | 1          | 102     | 9        |
| 4       | 2          | 102     | 6        |
| 5       | 3          | 102     | 10       |
| 6       | 1          | 102     | 6        |
+---------+------------+---------+----------+

Product table:
+------------+-------+
| product_id | price |
+------------+-------+
| 1          | 10    |
| 2          | 25    |
| 3          | 15    |
+------------+-------+

Output: 
+---------+------------+
| user_id | product_id |
+---------+------------+
| 101     | 3          |
| 102     | 1          |
| 102     | 2          |
| 102     | 3          |
+---------+------------+ 

Explanation: 
User 101:
    - Spent 10 * 10 = 100 on product 1.
    - Spent 7 * 15 = 105 on product 3.
User 101 spent the most money on product 3.

User 102:
    - Spent (9 + 7) * 10 = 150 on product 1.
    - Spent 6 * 25 = 150 on product 2.
    - Spent 10 * 15 = 150 on product 3.
User 102 spent the most money on products 1, 2, and 3.
```
<br>

> The first solution is taken from [here](https://github.com/kamyu104/LeetCode-Solutions/blob/master/MySQL/product-sales-analysis-iv.sql)
<br>

> The second solution is taken from [here](https://velog.io/@dev_taehyun/algorithm-leetcode-2324)
<br>

```SQL
CREATE TABLE Product (product_id int, price int, PRIMARY KEY (product_id));

CREATE TABLE Sales (sale_id int, product_id int, user_id int, quantity int, PRIMARY KEY (sale_id),
    FOREIGN KEY (product_id) REFERENCES Product (product_id));


INSERT INTO Product VALUES
(1, 10), (2, 25), (3, 15);

INSERT INTO Sales VALUES
(1, 1, 101, 10), (2, 3, 101, 7), (3, 1, 102, 9),
(4, 2, 102, 6), (5, 3, 102, 10), (6, 1, 102, 6);


WITH spending_cte AS (
    SELECT S.user_id, S.product_id,
          RANK() OVER (PARTITION BY S.user_id ORDER BY SUM(S.quantity * P.price) DESC) AS rnk
    FROM Sales S INNER JOIN Product P ON S.product_id = P.product_id
    GROUP BY S.user_id, S.product_id
    ORDER BY NULL
)
SELECT user_id, product_id
FROM spending_cte
WHERE rnk = 1;


SELECT user_id, product_id
FROM (
    SELECT S.user_id, S.product_id,
           RANK() OVER(PARTITION BY S.user_id ORDER BY SUM(P.price * S.quantity) DESC) AS spent_rank
    FROM Sales S JOIN Product P USING (product_id)
    GROUP BY S.user_id, S.product_id
) AS SalesRankings
WHERE spent_rank = 1;
```
***