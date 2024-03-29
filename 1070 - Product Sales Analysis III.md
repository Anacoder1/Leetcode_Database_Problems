```
Table: Sales

+-------------+-------+
| Column Name | Type  |
+-------------+-------+
| sale_id     | int   |
| product_id  | int   |
| year        | int   |
| quantity    | int   |
| price       | int   |
+-------------+-------+

sale_id is the primary key of this table.

product_id is a foreign key to Product table.

Note that the price is per unit.


Table: Product

+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| product_id   | int     |
| product_name | varchar |
+--------------+---------+

product_id is the primary key of this table.


Write an SQL query that selects the product id, year, quantity, and price for the first year of every product sold.

The query result format is in the following example:

Sales table:
+---------+------------+------+----------+-------+
| sale_id | product_id | year | quantity | price |
+---------+------------+------+----------+-------+ 
| 1       | 100        | 2008 | 10       | 5000  |
| 2       | 100        | 2009 | 12       | 5000  |
| 7       | 200        | 2011 | 15       | 9000  |
+---------+------------+------+----------+-------+

Product table:
+------------+--------------+
| product_id | product_name |
+------------+--------------+
| 100        | Nokia        |
| 200        | Apple        |
| 300        | Samsung      |
+------------+--------------+

Result table:
+------------+------------+----------+-------+
| product_id | first_year | quantity | price |
+------------+------------+----------+-------+ 
| 100        | 2008       | 10       | 5000  |
| 200        | 2011       | 15       | 9000  |
+------------+------------+----------+-------+


SELECT the corresponding fields from table `Sales`.

Use another `SELECT product_id, MIN(year)` to obtain the first year of every product sold.
```
<br>

```SQL
CREATE TABLE Product (product_id int, product_name varchar(32), PRIMARY KEY (product_id));

CREATE TABLE Sales (sale_id int, product_id int, year int, quantity int, price int, PRIMARY KEY (sale_id),
    FOREIGN KEY (product_id) REFERENCES Product (product_id));


INSERT INTO Product VALUES
(100, 'Nokia'), (200, 'Apple'), (300, 'Samsung');

INSERT INTO Sales VALUES
(1, 100, 2008, 10, 5000), (2, 100, 2009, 12, 5000),
(7, 200, 2011, 15, 9000);


SELECT product_id, year AS first_year, quantity, price
FROM Sales
WHERE (product_id, year) IN (
    SELECT product_id, MIN(year)
    FROM Sales
    GROUP BY product_id
);


SELECT product_id, year AS first_year, quantity, price
FROM Sales
WHERE (product_id, year) IN (
    SELECT product_id, MIN(year)
    FROM Sales
    GROUP BY 1
);
```
***