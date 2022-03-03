```
Table: Transactions

+-------------+------+
| Column Name | Type |
+-------------+------+
| account_id  | int  |
| day         | date |
| type        | ENUM |
| amount      | int  |
+-------------+------+

(account_id, day) is the primary key for this table.

Each row contains information about one transaction including the transaction type and the amount.

type is ENUM of the type ('Deposit','Withdraw') 


Write an SQL query to report the balance of each user after each transaction.

You may assume that the balance of each account before any transaction is 0 and you may assume that the balance will not be below 0 at any moment.

Return the result table ordered by account_id and day in ascending order.

The query result format is in the following example. 

(couldn’t find complete question anywhere)
```
<br>

> Solution taken from [here](https://github.com/kamyu104/LeetCode-Solutions/blob/master/MySQL/account-balance.sql)
<br>

```SQL
SELECT account_id, day,
SUM(CASE WHEN type = 'DEPOSIT' THEN amount ELSE -amount END)
OVER (PARTITION BY account_id ORDER BY day) AS balance
FROM Transactions;
```
***