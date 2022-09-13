```
Table: Customer

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| customer_id | int     |
| product_key | int     |
+-------------+---------+

product_key is a foreign key to Product table.


Table: Product

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| product_key | int     |
+-------------+---------+

product_key is the primary key column for this table.


Write a SQL query for a report that provides the customer ids from the Customer table that bought all the products in the Product table.

For example:

Customer table:
+-------------+-------------+
| customer_id | product_key |
+-------------+-------------+
| 1           | 5           |
| 2           | 6           |
| 3           | 5           |
| 3           | 6           |
| 1           | 6           |
+-------------+-------------+

Product table:
+-------------+
| product_key |
+-------------+
| 5           |
| 6           |
+-------------+

Result table:
+-------------+
| customer_id |
+-------------+
| 1           |
| 3           |
+-------------+

The customers who bought all the products (5 and 6) are customers with id 1 and 3.


First SELECT the number of products each customer bought from table Customer.

Next SELECT the number of products in table Product.

Finally JOIN the two selection results and return the customers that have bought the same number of products as the total number of products.
```
<br>

```SQL
CREATE TABLE Customer (customer_id int, product_key int);
CREATE TABLE Product (product_key int);

INSERT INTO Customer VALUES
(1, 5), (2, 6), (3, 5), (3, 6), (1, 6);

INSERT INTO Product VALUES
(5), (6);


SELECT DISTINCT customer_id
FROM (
    SELECT customer_id, COUNT(DISTINCT product_key) AS product_count
    FROM Customer
    GROUP BY customer_id
) AS customer_product_counts
JOIN (
    SELECT COUNT(DISTINCT product_key) AS product_count
    FROM Product
) AS product_counts
ON customer_product_counts.product_count = product_counts.product_count;


SELECT customer_id
FROM Customer
GROUP BY customer_id
HAVING COUNT(DISTINCT product_key) = (
    SELECT COUNT(DISTINCT product_key)
    FROM Product
);
```
***