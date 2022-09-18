```
Table Accounts:

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| name          | varchar |
+---------------+---------+

the id is the primary key for this table.

This table contains the account id and the user name of each account.


Table Logins:

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| login_date    | date    |
+---------------+---------+

There is no primary key for this table, it may contain duplicates.

This table contains the account id of the user who logged in and the login date.
A user may log in multiple times in the day.


Write a SQL query to find the id and the name of active users.

Active users are those who logged in to their accounts for 5 or more consecutive days.

Return the result table ordered by the id.

The query result format is in the following example:

Accounts table:
+----+----------+
| id | name     |
+----+----------+
| 1  | Winston  |
| 7  | Jonathan |
+----+----------+

Logins table:
+----+------------+
| id | login_date |
+----+------------+
| 7  | 2020-05-30 |
| 1  | 2020-05-30 |
| 7  | 2020-05-31 |
| 7  | 2020-06-01 |
| 7  | 2020-06-02 |
| 7  | 2020-06-02 |
| 7  | 2020-06-03 |
| 1  | 2020-06-07 |
| 7  | 2020-06-10 |
+----+------------+

Result table:
+----+----------+
| id | name     |
+----+----------+
| 7  | Jonathan |
+----+----------+
User Winston with id = 1 logged in 2 times only in 2 different days, so, Winston is not an active user.
User Jonathan with id = 7 logged in 7 times in 6 different days, five of them were consecutive days, so, Jonathan is an active user


Follow up question:
Can you write a general solution if the active users are those who logged in to their accounts for n or more consecutive days?


For each `id`, if there are at least 5 consecutive values of `login_date` in table `Logins`,
then `id` should be selected.

SELECT such values of `id` and the corresponding value of `name` from `Accounts` table.
```
<br>

> The third solution is taken from [here](https://blog.csdn.net/betty1121/article/details/108647931)
<br>

> The fourth and fifth solutions are taken from [here](https://shreyash1811.github.io/sql/sql_30day_challenge_25to29/)
<br>

```SQL
CREATE TABLE Accounts (id int, name varchar(32), PRIMARY KEY (id));

CREATE TABLE Logins (id int, login_date date,
    FOREIGN KEY (id) REFERENCES Accounts (id));
    
    
INSERT INTO Accounts VALUES
(1, 'Winston'), (7, 'Jonathan');

INSERT INTO Logins VALUES
(7, '2020-05-30'), (1, '2020-05-30'), (7, '2020-05-31'),
(7, '2020-06-01'), (7, '2020-06-02'), (7, '2020-06-02'),
(7, '2020-06-03'), (1, '2020-06-07'), (7, '2020-06-10');


SELECT id, name
FROM Accounts WHERE id IN (
    SELECT L1.id
    FROM Logins L1, Logins L2, Logins L3, Logins L4, Logins L5
    WHERE L1.id = L2.id AND L2.id = L3.id AND L3.id = L4.id AND L4.id = L5.id
    AND DATEDIFF(L2.login_date, L1.login_date) = 1
    AND DATEDIFF(L3.login_date, L2.login_date) = 1
    AND DATEDIFF(L4.login_date, L3.login_date) = 1
    AND DATEDIFF(L5.login_date, L4.login_date) = 1
)
ORDER BY id;


SELECT A.id, A.name
FROM Accounts A
WHERE A.id IN (
    SELECT L.id
    FROM (
        SELECT DISTINCT L.id, L.login_date,
               DATEDIFF(L.login_date, '1970-01-01') - DENSE_RANK() OVER(PARTITION BY L.id ORDER BY L.login_date) AS rk_group
        FROM Logins L
    ) L
    GROUP BY L.rk_group, L.id
    HAVING COUNT(L.login_date) >= 5
)
ORDER BY A.id;


WITH temp AS (
    SELECT DISTINCT id, login_date,
           DENSE_RANK() OVER (PARTITION BY id ORDER BY login_date) AS rnk
    FROM Logins
)
SELECT DISTINCT id, name
FROM Accounts
WHERE id IN 
(
    SELECT T1.id
    FROM temp T1 JOIN temp T2
    ON T2.rnk = T1.rnk + 4 AND T1.id = T2.id
    WHERE DATEDIFF(T2.login_date, T1.login_date) = 4
);


WITH RECURSIVE rec_t AS (
    SELECT id, login_date, 1 AS days
    FROM Logins
    UNION ALL
    SELECT L.id, L.login_date, rec_t.days + 1
    FROM rec_t
    INNER JOIN Logins L
    ON rec_t.id = L.id AND DATE_ADD(rec_t.login_date, INTERVAL 1 DAY) = L.login_date
)
SELECT *
FROM Accounts
WHERE id IN (
    SELECT DISTINCT id
    FROM rec_t
    WHERE days = 5
)
ORDER BY id;


SELECT DISTINCT L1.id, (
    SELECT name
    FROM Accounts
    WHERE id = L1.id
) AS name
FROM Logins L1 JOIN Logins L2
ON L1.id = L2.id AND DATEDIFF(L2.login_date, L1.login_date) BETWEEN 1 AND 4
GROUP BY L1.id, L1.login_date
HAVING COUNT(DISTINCT L2.login_date) = 4;
```
***