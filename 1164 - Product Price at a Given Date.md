```
Table: Products

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| product_id    | int     |
| new_price     | int     |
| change_date   | date    |
+---------------+---------+

(product_id, change_date) is the primary key of this table.

Each row of this table indicates that the price of some product was changed to a new price at some date.


Write an SQL query to find the prices of all products on 2019-08-16.

Assume the price of all products before any change is 10.

The query result format is in the following example:

Products table:
+------------+-----------+-------------+
| product_id | new_price | change_date |
+------------+-----------+-------------+
| 1          | 20        | 2019-08-14  |
| 2          | 50        | 2019-08-14  |
| 1          | 30        | 2019-08-15  |
| 1          | 35        | 2019-08-16  |
| 2          | 65        | 2019-08-17  |
| 3          | 20        | 2019-08-18  |
+------------+-----------+-------------+

Result table:
+------------+-------+
| product_id | price |
+------------+-------+
| 2          | 50    |
| 1          | 35    |
| 3          | 10    |
+------------+-------+


For each product, consider two cases.

The first case is that there is at least one entry of the product and the new price that has change date before or on 2019-08-16.

The second case is that there is no entry of the product and the new price that has change date before on 2019-08-16.

For the first case, select product_id and new_price as price from table Products where change_date is the maximum value less than or equal to 2019-08-16.

For the second case, select product_id and 10 as price from table Products where product_id is not in the query that change_date <= '2019-08-16.

Finally, join the two sub-queries to obtain the final result.
```
<br>

```SQL
CREATE TABLE Products (product_id int, new_price int, change_date date);

INSERT INTO Products VALUES
(1, 20, '2019-08-14'), (2, 50, '2019-08-14'),
(1, 30, '2019-08-15'), (1, 35, '2019-08-16'),
(2, 65, '2019-08-17'), (3, 20, '2019-08-18');


SELECT * FROM (
    SELECT product_id, new_price AS price
    FROM Products
    WHERE (product_id, change_date) IN (
        SELECT product_id, MAX(change_date)
        FROM Products
        WHERE change_date <= '2019-08-16'
        GROUP BY product_id
    )
    UNION
    SELECT DISTINCT product_id, 10 AS price
    FROM Products
    WHERE product_id NOT IN (
        SELECT product_id
        FROM Products
        WHERE change_date <= '2019-08-16'
    )
) union_result;


SELECT P1.product_id, P1.new_price AS price
FROM Products P1
WHERE P1.change_date <= '2019-08-16' AND
(product_id, DATEDIFF('2019-08-16', P1.change_date)) IN (
    SELECT product_id, MIN(DATEDIFF('2019-08-16', change_date))
    FROM Products
    WHERE change_date <= '2019-08-16'
    GROUP BY product_id
)
UNION
SELECT product_id, 10 AS price
FROM Products
GROUP BY product_id
HAVING MIN(change_date) > '2019-08-16'
ORDER BY price DESC;
```
***