```
Table: Customers

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| customer_id   | int     |
| name          | varchar |
+---------------+---------+

customer_id is the primary key for this table.

This table contains information about the customers.


Table: Orders

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| order_id      | int     |
| order_date    | date    |
| customer_id   | int     |
| product_id    | int     |
+---------------+---------+

order_id is the primary key for this table.

This table contains information about the orders made by customer_id.

There will be no product ordered by the same user more than once in one day.


Table: Products

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| product_id    | int     |
| product_name  | varchar |
| price         | int     |
+---------------+---------+

product_id is the primary key for this table.

This table contains information about the Products.


Write an SQL query to find the most recent order(s) of each product.

Return the result table sorted by product_name in ascending order and in case of a tie by the product_id in ascending order.

If there still a tie, order them by the order_id in ascending order.

The query result format is in the following example:

Customers
+-------------+-----------+
| customer_id | name      |
+-------------+-----------+
| 1           | Winston   |
| 2           | Jonathan  |
| 3           | Annabelle |
| 4           | Marwan    |
| 5           | Khaled    |
+-------------+-----------+

Orders
+----------+------------+-------------+------------+
| order_id | order_date | customer_id | product_id |
+----------+------------+-------------+------------+
| 1        | 2020-07-31 | 1           | 1          |
| 2        | 2020-07-30 | 2           | 2          |
| 3        | 2020-08-29 | 3           | 3          |
| 4        | 2020-07-29 | 4           | 1          |
| 5        | 2020-06-10 | 1           | 2          |
| 6        | 2020-08-01 | 2           | 1          |
| 7        | 2020-08-01 | 3           | 1          |
| 8        | 2020-08-03 | 1           | 2          |
| 9        | 2020-08-07 | 2           | 3          |
| 10       | 2020-07-15 | 1           | 2          |
+----------+------------+-------------+------------+

Products
+------------+--------------+-------+
| product_id | product_name | price |
+------------+--------------+-------+
| 1          | keyboard     | 120   |
| 2          | mouse        | 80    |
| 3          | screen       | 600   |
| 4          | hard disk    | 450   |
+------------+--------------+-------+

Result table:
+--------------+------------+----------+------------+
| product_name | product_id | order_id | order_date |
+--------------+------------+----------+------------+
| keyboard     | 1          | 6        | 2020-08-01 |
| keyboard     | 1          | 7        | 2020-08-01 |
| mouse        | 2          | 8        | 2020-08-03 |
| screen       | 3          | 3        | 2020-08-29 |
+--------------+------------+----------+------------+

keyboard's most recent order is in 2020-08-01, it was ordered two times this day.

mouse's most recent order is in 2020-08-03, it was ordered only once this day.

screen's most recent order is in 2020-08-29, it was ordered only once this day.

The hard disk was never ordered and we don't include it in the result table.


JOIN tables Products and Orders using the field product_id. 

For each product, find the maximum order date and find the orders that have order_date equal to the maximum order date. 

Finally, sort the result table using product_name, product_id and order_id
```
<br>

```SQL
CREATE TABLE Customers (customer_id int, name varchar(32));
CREATE TABLE Orders (order_id int, order_date date, customer_id int, product_id int);
CREATE TABLE Products (product_id int, product_name varchar(32), price int);

INSERT INTO Customers VALUES
(1, 'Winston'), (2, 'Jonathan'), (3, 'Annabelle'),
(4, 'Marwan'), (5, 'Khaled');

INSERT INTO Orders VALUES
(1, '2020-07-31', 1, 1), (2, '2020-07-30', 2, 2),
(3, '2020-08-29', 3, 3), (4, '2020-07-29', 4, 1),
(5, '2020-06-10', 1, 2), (6, '2020-08-01', 2, 1),
(7, '2020-08-01', 3, 1), (8, '2020-08-03', 1, 2),
(9, '2020-08-07', 2, 3), (10, '2020-07-15', 1, 2);

INSERT INTO Products VALUES
(1, 'keyboard', 120), (2, 'mouse', 80),
(3, 'screen', 600), (4, 'hard disk', 450);


SELECT product_name, Products.product_id, order_id, order_date
FROM Products JOIN Orders ON Products.product_id = Orders.product_id
WHERE (Orders.product_id, order_date) IN (
    SELECT product_id, MAX(order_date)
    FROM Orders
    GROUP BY product_id
)
ORDER BY product_name, product_id, order_id;


SELECT P.product_name, O.product_id, O.order_id, O.order_date
FROM Orders O LEFT JOIN Products P ON O.product_id = P.product_id
JOIN (
    SELECT product_id, MAX(order_date) AS 'recent_date'
    FROM Orders
    GROUP BY product_id
) M
ON M.product_id = O.product_id AND M.recent_date = O.order_date
ORDER BY P.product_name, O.product_id, O.order_id;
```
***