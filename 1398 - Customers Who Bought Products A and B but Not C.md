```
Table: Customers

+---------------------+---------+
| Column Name         | Type    |
+---------------------+---------+
| customer_id         | int     |
| customer_name       | varchar |
+---------------------+---------+

customer_id is the primary key for this table.

customer_name is the name of the customer.


Table: Orders

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| order_id      | int     |
| customer_id   | int     |
| product_name  | varchar |
+---------------+---------+

order_id is the primary key for this table.

customer_id is the id of the customer who bought the product "product_name".


Write an SQL query to report the customer_id and customer_name of customers who bought products “A”, “B” but did not buy the product “C” since we want to recommend them buy this product.

Return the result table ordered by customer_id.

The query result format is in the following example.

Customers table:
+-------------+---------------+
| customer_id | customer_name |
+-------------+---------------+
| 1           | Daniel        |
| 2           | Diana         |
| 3           | Elizabeth     |
| 4           | Jhon          |
+-------------+---------------+

Orders table:
+------------+--------------+---------------+
| order_id   | customer_id  | product_name  |
+------------+--------------+---------------+
| 10         |     1        |     A         |
| 20         |     1        |     B         |
| 30         |     1        |     D         |
| 40         |     1        |     C         |
| 50         |     2        |     A         |
| 60         |     3        |     A         |
| 70         |     3        |     B         |
| 80         |     3        |     D         |
| 90         |     4        |     C         |
+------------+--------------+---------------+

Result table:
+-------------+---------------+
| customer_id | customer_name |
+-------------+---------------+
| 3           | Elizabeth     |
+-------------+---------------+

Only the customer_id with id 3 bought the product A and B but not the product C.


Join the tables Customers and Orders using customer_id.

Use HAVING SUM to SELECT the customers that bought products A and B but not C.

The sums of A and B are both greater than 0 and the sum of C is 0.
```
<br>

```SQL
CREATE TABLE Customers (customer_id int, customer_name varchar(32));
CREATE TABLE Orders (order_id int, customer_id int, product_name varchar(32));

INSERT INTO Customers VALUES
(1, 'Daniel'), (2, 'Diana'),
(3, 'Elizabeth'), (4, 'Jhon');

INSERT INTO Orders VALUES
(10, 1, 'A'), (20, 1, 'B'), (30, 1, 'D'),
(40, 1, 'C'), (50, 2, 'A'), (60, 3, 'A'),
(70, 3, 'B'), (80, 3, 'D'), (90, 4, 'C');


SELECT Customers.customer_id, customer_name
FROM Customers LEFT JOIN Orders ON Customers.customer_id = Orders.customer_id
GROUP BY Customers.customer_id, customer_name
HAVING SUM(product_name = 'A') > 0 AND SUM(product_name = 'B') > 0 AND SUM(product_name = 'C') = 0
ORDER BY Customers.customer_id;


SELECT customer_id, customer_name
FROM Customers C
WHERE customer_id IN (
    SELECT DISTINCT customer_id
    FROM Orders
    WHERE product_name = 'A' AND customer_id IN (
        SELECT DISTINCT customer_id FROM Orders
        WHERE product_name = 'B'
    )
)
AND customer_id NOT IN (
    SELECT DISTINCT customer_id
    FROM Orders
    WHERE product_name = 'C'
);
```
***