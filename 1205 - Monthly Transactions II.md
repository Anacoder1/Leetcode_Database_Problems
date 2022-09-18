```
Table: Transactions

+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| id             | int     |
| country        | varchar |
| state          | enum    |
| amount         | int     |
| trans_date     | date    |
+----------------+---------+

id is the primary key of this table.

The table has information about incoming transactions.

The state column is an enum of type ["approved", "declined"].


Table: Chargebacks

+----------------+---------+
| Column Name    | Type    |
+----------------+---------+
| trans_id       | int     |
| charge_date    | date    |
+----------------+---------+

Chargebacks contains basic information regarding incoming chargebacks from some transactions placed in Transactions table.

trans_id is a foreign key to the id column of Transactions table.

Each chargeback corresponds to a transaction made previously even if they were not approved.


Write a SQL query to find for each month and country, the number of approved transactions and their total amount, the number of chargebacks and their total amount.

Note: In your query, given the month and country, ignore rows with all zeros.

The query result format is in the following example:

Transactions table:
+------+---------+----------+--------+------------+
| id   | country | state    | amount | trans_date |
+------+---------+----------+--------+------------+
| 101  | US      | approved | 1000   | 2019-05-18 |
| 102  | US      | declined | 2000   | 2019-05-19 |
| 103  | US      | approved | 3000   | 2019-06-10 |
| 104  | US      | approved | 4000   | 2019-06-13 |
| 105  | US      | approved | 5000   | 2019-06-15 |
+------+---------+----------+--------+------------+

Chargebacks table:
+------------+------------+
| trans_id   | trans_date |
+------------+------------+
| 102        | 2019-05-29 |
| 101        | 2019-06-30 |
| 105        | 2019-09-18 |
+------------+------------+

Result table:
+----------+---------+----------------+-----------------+-------------------+--------------------+
| month    | country | approved_count | approved_amount | chargeback_count  | chargeback_amount  |
+----------+---------+----------------+-----------------+-------------------+--------------------+
| 2019-05  | US      | 1              | 1000            | 1                 | 2000               |
| 2019-06  | US      | 3              | 12000           | 1                 | 1000               |
| 2019-09  | US      | 0              | 0               | 1                 | 5000               |
+----------+---------+----------------+-----------------+-------------------+--------------------+


Use `DATE_FORMAT(trans_date, '%Y-%m')` to obtain the year and month of each transaction.

For approved transactions and chargeback transactions, SELECT the entries respectively.

As for chargeback transactions, do INNER JOIN on tables `Transactions` and `Chargebacks`
using `id` in `Transactions` and `trans_id` in `Chargebacks`.

Use `UNION ALL` to union the selected results.

Obtain the values of the required fields as the final result.
```
<br>

```SQL
CREATE TABLE Transactions (id int, country varchar(32), state enum('approved', 'declined'), amount int, trans_date date, PRIMARY KEY(id));

CREATE TABLE Chargebacks (trans_id int, charge_date date,
    FOREIGN KEY (trans_id) REFERENCES Transactions (id));
    
INSERT INTO Transactions VALUES
(101, 'US', 'approved', 1000, '2019-05-18'),
(102, 'US', 'declined', 2000, '2019-05-19'),
(103, 'US', 'approved', 3000, '2019-06-10'),
(104, 'US', 'approved', 4000, '2019-06-13'),
(105, 'US', 'approved', 5000, '2019-06-15');

INSERT INTO Chargebacks VALUES
(102, '2019-05-29'), (101, '2019-06-30'),
(105, '2019-09-18');


SELECT ym AS month, country, 
      SUM(IF(state = 'approved', 1, 0)) AS approved_count,
      SUM(IF(state = 'approved', amount, 0)) AS approved_amount,
      SUM(IF(state = 'chargeback', 1, 0)) AS chargeback_count,
      SUM(IF(state = 'chargeback', amount, 0)) AS chargeback_amount
FROM (
    (
        SELECT DATE_FORMAT(trans_date, '%Y-%m') AS ym,
              country, amount, 'approved' AS state
        FROM Transactions
        WHERE state = 'approved'
    ) UNION ALL (
        SELECT DATE_FORMAT(Chargebacks.charge_date, '%Y-%m') AS ym,
              country, amount, 'chargeback' AS state
        FROM Transactions INNER JOIN Chargebacks
        ON Transactions.id = Chargebacks.trans_id
    )
) temp
GROUP BY ym, country;


SELECT DATE_FORMAT(charge_date, '%Y-%m') AS month, country,
       SUM(CASE WHEN state = 'approved' THEN 1 ELSE 0 END) AS approved_count,
       SUM(CASE WHEN state = 'approved' THEN amount ELSE 0 END) AS approved_amount,
       SUM(CASE WHEN state = 'chargeback' THEN 1 ELSE 0 END) AS chargeback_count,
       SUM(CASE WHEN state = 'chargeback' THEN amount ELSE 0 END) AS chargeback_amount
FROM (
    SELECT C.trans_id, T.country, 'chargeback' AS state, T.amount, C.charge_date
    FROM Chargebacks C JOIN Transactions T ON C.trans_id = T.id
    UNION ALL
    SELECT * FROM Transactions
) AS T1
GROUP BY country, month
HAVING approved_amount > 0 OR chargeback_amount > 0;
```
***