```
Table: Transactions

+----------------+----------+
| Column Name    | Type     |
+----------------+----------+
| transaction_id | int      |
| day            | datetime |
| amount         | int      |
+----------------+----------+

transaction_id is the primary key for this table.

Each row contains information about one transaction.


Write an SQL query to report the IDs of the transactions with the maximum amount on their respective day. 

If in one day there are multiple such transactions, return all of them.

Return the result table in ascending order by transaction_id.

The query result format is in the following example:

Transactions table:
+----------------+--------------------+--------+
| transaction_id | day                | amount |
+----------------+--------------------+--------+
| 8              | 2021-4-3 15:57:28  | 57     |
| 9              | 2021-4-28 08:47:25 | 21     |
| 1              | 2021-4-29 13:28:30 | 58     |
| 5              | 2021-4-28 16:39:59 | 40     |
| 6              | 2021-4-29 23:39:28 | 58     |
+----------------+--------------------+--------+

Result table:
+----------------+
| transaction_id |
+----------------+
| 1              |
| 5              |
| 6              |
| 8              |
+----------------+

"2021-4-3"  --> We have one transaction with ID 8, so we add 8 to the result table.

"2021-4-28" --> We have two transactions with IDs 5 and 9. The transaction with ID 5 has an amount of 40, while the transaction with ID 9 has an amount of 21. We only include the transaction with ID 5 as it has the maximum amount this day.

"2021-4-29" --> We have two transactions with IDs 1 and 6. Both transactions have the same amount of 58, so we include both in the result table.

We order the result table by transaction_id after collecting these IDs.


Use RANK() and PARTITION BY to order each date’s amounts in descending order & find the max amt. for each date.

Then select transaction_id for these entries.
```
<br>

> Second solution taken from [here](https://zqt0.gitbook.io/leetcode/sql/1831.maximum-transaction-each-day)
<br>

```SQL
SELECT transaction_id
FROM (
    SELECT transaction_id, DATE(day) AS date1, amount,
    RANK() OVER (PARTITION BY DATE(day) ORDER BY amount DESC) AS rnk
    FROM Transactions
) AS temp
WHERE rnk = 1
ORDER BY transaction_id;


SELECT transaction_id
FROM (
    SELECT *, RANK() OVER(PARTITION BY DATE(day) ORDER BY amount DESC) rk
    FROM Transactions
) a
WHERE rk = 1
ORDER BY 1;
```
***