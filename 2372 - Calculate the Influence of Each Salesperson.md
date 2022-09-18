```
Table: Salesperson

+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| salesperson_id | int     |
| name           | varchar |
+----------------+---------+

salesperson_id is the primary key for this table.

Each row in this table shows the ID of a salesperson.

 
Table: Customer

+----------------+------+
| Column Name    | Type |
+----------------+------+
| customer_id    | int  |
| salesperson_id | int  |
+----------------+------+

customer_id is the primary key for this table.

salesperson_id is a foreign key from the Salesperson table.

Each row in this table shows the ID of a customer and the ID of the salesperson. 

 
Table: Sales

+-------------+------+
| Column Name | Type |
+-------------+------+
| sale_id     | int  |
| customer_id | int  |
| price       | int  |
+-------------+------+

sale_id is the primary key for this table.

customer_id is a foreign key from the Customer table.

Each row in this table shows ID of a customer and the price they paid for the sale with sale_id.

 
Write a SQL query to report the sum of prices paid by the customers of each salesperson.
If a salesperson does not have any customers, the total value should be 0.

Return the result table in any order.

The query result format is shown in the following example.

Example 1:

Input:

Salesperson table:
+----------------+-------+
| salesperson_id | name  |
+----------------+-------+
| 1              | Alice |
| 2              | Bob   |
| 3              | Jerry |
+----------------+-------+

Customer table:
+-------------+----------------+
| customer_id | salesperson_id |
+-------------+----------------+
| 1           | 1              |
| 2           | 1              |
| 3           | 2              |
+-------------+----------------+

Sales table:
+---------+-------------+-------+
| sale_id | customer_id | price |
+---------+-------------+-------+
| 1       | 2           | 892   |
| 2       | 1           | 354   |
| 3       | 3           | 988   |
| 4       | 3           | 856   |
+---------+-------------+-------+

Output: 
+----------------+-------+-------+
| salesperson_id | name  | total |
+----------------+-------+-------+
| 1              | Alice | 1246  |
| 2              | Bob   | 1844  |
| 3              | Jerry | 0     |
+----------------+-------+-------+

Explanation: 
Alice is the salesperson for customers 1 and 2.
  - Customer 1 made one purchase with 354.
  - Customer 2 made one purchase with 892.

The total for Alice is 354 + 892 = 1246.

Bob is the salesperson for customers 3.
  - Customer 1 made one purchase with 988 and 856.
The total for Bob is 988 + 856 = 1844.

Jerry is not the salesperson of any customer.
The total for Jerry is 0.

```
<br>

> The first solution is taken from [here](https://github.com/kamyu104/LeetCode-Solutions/blob/master/MySQL/calculate-the-influence-of-each-salesperson.sql)
<br>

```SQL
CREATE TABLE Salesperson (salesperson_id int, name varchar(32), PRIMARY KEY (salesperson_id));

CREATE TABLE Customer (customer_id int, salesperson_id int, PRIMARY KEY (customer_id),
    FOREIGN KEY (salesperson_id) REFERENCES Salesperson (salesperson_id));
    
CREATE TABLE Sales (sale_id int, customer_id int, price int, PRIMARY KEY (sale_id),
    FOREIGN KEY (customer_id) REFERENCES Customer (customer_id));
    
    
INSERT INTO Salesperson VALUES
(1, 'Alice'), (2, 'Bob'), (3, 'Jerry');

INSERT INTO Customer VALUES
(1, 1), (2, 1), (3, 2);

INSERT INTO Sales VALUES
(1, 2, 892), (2, 1, 354), (3, 3, 988), (4, 3, 856);


SELECT SP.salesperson_id, SP.name, SUM(IFNULL(S.price, 0)) AS total
FROM Salesperson SP
LEFT JOIN Customer C ON SP.salesperson_id = C.salesperson_id
LEFT JOIN Sales S ON C.customer_id = S.customer_id
GROUP BY SP.salesperson_id, SP.name
ORDER BY NULL;

-- For the above solution, IFNULL(SUM(S.price), 0) works the same way as SUM(IFNULL(S.price, 0))
```
***