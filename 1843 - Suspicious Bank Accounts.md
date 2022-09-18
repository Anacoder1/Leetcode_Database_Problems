```
Table: Accounts

+----------------+------+
| Column Name    | Type |
+----------------+------+
| account_id     | int  |
| max_income     | int  |
+----------------+------+

account_id is the primary key for this table.

Each row contains information about the maximum monthly income for one bank account.


Table: Transactions

+----------------+----------+
| Column Name    | Type     |
+----------------+----------+
| transaction_id | int      |
| account_id     | int      |
| type           | ENUM     |
| amount         | int      |
| day            | datetime |
+----------------+----------+

transaction_id is the primary key for this table.

Each row contains information about one transaction.

type is ENUM ('Creditor','Debtor') where 'Creditor' means the user deposited money into their account and 'Debtor' means the user withdrew money from their account.

amount is the amount of money depositied / withdrawn during the transaction.


Write a SQL query to report the IDs of all suspicious bank accounts.

A bank account is suspicious if the total income exceeds the max_income for this account for two or more consecutive months.

The total income of an account in some month is the sum of all its deposits in that month (i.e., transactions of the type 'Creditor').

Return the result table in ascending order by transaction_id.

The query result format is in the following example:

Accounts table:
+------------+------------+
| account_id | max_income |
+------------+------------+
| 3          | 21000      |
| 4          | 10400      |
+------------+------------+

Transactions table:
+----------------+------------+----------+--------+---------------------+
| transaction_id | account_id | type     | amount | day                 |
+----------------+------------+----------+--------+---------------------+
| 2              | 3          | Creditor | 107100 | 2021-06-02 11:38:14 |
| 4              | 4          | Creditor | 10400  | 2021-06-20 12:39:18 |
| 11             | 4          | Debtor   | 58800  | 2021-07-23 12:41:55 |
| 1              | 4          | Creditor | 49300  | 2021-05-03 16:11:04 |
| 15             | 3          | Debtor   | 75500  | 2021-05-23 14:40:20 |
| 10             | 3          | Creditor | 102100 | 2021-06-15 10:37:16 |
| 14             | 4          | Creditor | 56300  | 2021-07-21 12:12:25 |
| 19             | 4          | Debtor   | 101100 | 2021-05-09 15:21:49 |
| 8              | 3          | Creditor | 64900  | 2021-07-26 15:09:56 |
| 7              | 3          | Creditor | 90900  | 2021-06-14 11:23:07 |
+----------------+------------+----------+--------+---------------------+

Result table:
+------------+
| account_id |
+------------+
| 3          |
+------------+

For account 3:
- In 6-2021, the user had an income of 107100 + 102100 + 90900 = 300100.
- In 7-2021, the user had an income of 64900.
We can see that the income exceeded the max income of 21000 for two consecutive months, so we include 3 in the result table.

For account 4:
- In 5-2021, the user had an income of 49300.
- In 6-2021, the user had an income of 10400.
- In 7-2021, the user had an income of 56300.
We can see that the income exceeded the max income in May and July, but not in June.
Since the account did not exceed the max income for two consecutive months, we do not include it in the result table.


For each account_id, calculate each month's total income.

Then return the accounts that have at least 2 consecutive months with total income exceeding max_income.
```
<br>

```SQL
CREATE TABLE Accounts (account_id int, max_income int, PRIMARY KEY (account_id));

CREATE TABLE Transactions (transaction_id int, account_id int, type enum('Creditor', 'Debtor'), amount int, day datetime, PRIMARY KEY (transaction_id),
    FOREIGN KEY (account_id) REFERENCES Accounts (account_id));
    
INSERT INTO Accounts VALUES
(3, 21000), (4, 10400);

INSERT INTO Transactions VALUES
(2, 3, 'Creditor', 107100, '2021-06-02 11:38:14'),
(4, 4, 'Creditor', 10400, '2021-06-20 12:39:18'),
(11, 4, 'Debtor', 58800, '2021-07-23 12:41:55'),
(1, 4, 'Creditor', 49300, '2021-05-03 16:11:04'),
(15, 3, 'Debtor', 75500, '2021-05-23 14:40:20'),
(10, 3, 'Creditor', 102100, '2021-06-15 10:37:16'),
(14, 4, 'Creditor', 56300, '2021-07-21 12:12:25'),
(19, 4, 'Debtor', 101100, '2021-05-09 15:21:49'),
(8, 3, 'Creditor', 64900, '2021-07-26 15:09:56'),
(7, 3, 'Creditor', 90900, '2021-06-14 11:23:07');


WITH temp AS (
    SELECT T.account_id, DATE_FORMAT(day, "%Y%m01") AS ym,
           SUM(IF(type = 'Creditor', amount, 0)) AS sumcred,
           A.max_income, T.transaction_id
    FROM Transactions T JOIN Accounts A
    ON T.account_id = A.account_id
    GROUP BY T.account_id, ym, T.transaction_id
    ORDER BY T.transaction_id
)
SELECT DISTINCT T1.account_id
FROM temp T1 JOIN temp T2
ON DATEDIFF(T2.ym, T1.ym) BETWEEN 28 AND 31
AND T1.account_id = T2.account_id
AND T1.sumcred > T1.max_income
AND T2.sumcred > T2.max_income;


SELECT DISTINCT(account_id)
FROM (
    SELECT T.account_id,
           SUM(amount) - A.max_income AS diff_income,
           EXTRACT(month FROM day) AS day,
           EXTRACT(month FROM LEAD(day) OVER (PARTITION BY T.account_id ORDER BY day)) AS next_day
    FROM Transactions T JOIN Accounts A
    ON T.account_id = A.account_id
    WHERE type = 'Creditor'
    GROUP BY T.account_id, LEFT(day, 7), day, transaction_id
    HAVING diff_income > 0
    ORDER BY transaction_id
) T1
WHERE day + 1 = next_day;
```
***