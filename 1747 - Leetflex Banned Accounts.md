```
Table: LogInfo

+-------------+----------+
| Column Name | Type     |
+-------------+----------+
| account_id  | int      |
| ip_address  | int      |
| login       | datetime |
| logout      | datetime |
+-------------+----------+

There is no primary key for this table, and it may contain duplicates.

The table contains information about the login and logout dates of Leetflex accounts.

It also contains the IP address from which the account logged in and out.

It is guaranteed that the logout time is after the login time.


Write an SQL query to find the account_id of the accounts that should be banned from Leetflex.

An account should be banned if it was logged in at some moment from two different IP addresses.

Return the result table in any order.

The query result format is in the following example:

LogInfo table:
+------------+------------+---------------------+---------------------+
| account_id | ip_address | login               | logout              |
+------------+------------+---------------------+---------------------+
| 1          | 1          | 2021-02-01 09:00:00 | 2021-02-01 09:30:00 |
| 1          | 2          | 2021-02-01 08:00:00 | 2021-02-01 11:30:00 |
| 2          | 6          | 2021-02-01 20:30:00 | 2021-02-01 22:00:00 |
| 2          | 7          | 2021-02-02 20:30:00 | 2021-02-02 22:00:00 |
| 3          | 9          | 2021-02-01 16:00:00 | 2021-02-01 16:59:59 |
| 3          | 13         | 2021-02-01 17:00:00 | 2021-02-01 17:59:59 |
| 4          | 10         | 2021-02-01 16:00:00 | 2021-02-01 17:00:00 |
| 4          | 11         | 2021-02-01 17:00:00 | 2021-02-01 17:59:59 |
+------------+------------+---------------------+---------------------+

Result table:
+------------+
| account_id |
+------------+
| 1          |
| 4          |
+------------+


Account ID 1 --> The account was active from "2021-02-01 09:00:00" to "2021-02-01 09:30:00" with two different IP addresses (1 and 2).
It should be banned.

Account ID 2 --> The account was active from two different addresses (6, 7) but in two different times.

Account ID 3 --> The account was active from two different addresses (9, 13) on the same day but they do not intersect at any moment.

Account ID 4 --> The account was active from "2021-02-01 17:00:00" to "2021-02-01 17:00:00" with two different IP addresses (10 and 11).
It should be banned.


For each account_id, it should be included in the result if and only if there are two entries of the account_id that have different ip_address values and the [login, logout] periods have intersection.

SELECT the values of account_id accordingly.
```
<br>

```SQL
CREATE TABLE LogInfo (account_id int, ip_address int, login datetime, logout datetime);

INSERT INTO LogInfo VALUES
(1, 1, '2021-02-01 09:00:00', '2021-02-01 09:30:00'),
(1, 2, '2021-02-01 08:00:00', '2021-02-01 11:30:00'),
(2, 6, '2021-02-01 20:30:00', '2021-02-01 22:00:00'),
(2, 7, '2021-02-02 20:30:00', '2021-02-02 22:00:00'),
(3, 9, '2021-02-01 16:00:00', '2021-02-01 16:59:59'),
(3, 13, '2021-02-01 17:00:00', '2021-02-01 17:59:59'),
(4, 10, '2021-02-01 16:00:00', '2021-02-01 17:00:00'),
(4, 11, '2021-02-01 17:00:00', '2021-02-01 17:59:59');


SELECT DISTINCT L1.account_id
FROM LogInfo L1 JOIN LogInfo L2 ON L1.account_id = L2.account_id
WHERE L1.ip_address != L2.ip_address AND (L1.logout >= L2.login AND L1.login <= L2.logout);


SELECT DISTINCT L1.account_id
FROM LogInfo L1 JOIN LogInfo L2 ON L1.account_id = L2.account_id AND L1.ip_address != L2.ip_address
WHERE NOT (L1.login > L2.logout OR L1.logout < L2.login);
```
***