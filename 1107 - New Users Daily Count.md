```
Table: Traffic

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| user_id       | int     |
| activity      | enum    |
| activity_date | date    |
+---------------+---------+

There is no primary key for this table, it may have duplicate rows.

The activity column is an ENUM type of ('login', 'logout', 'jobs', 'groups', 'homepage').


Write a SQL query that reports for every date within at most 90 days from today, the number of users that logged in for the first time on that date.
Assume today is 2019-06-30.

The query result format is in the following example:

Traffic table:
+---------+----------+---------------+
| user_id | activity | activity_date |
+---------+----------+---------------+
| 1       | login    | 2019-05-01    |
| 1       | homepage | 2019-05-01    |
| 1       | logout   | 2019-05-01    |
| 2       | login    | 2019-06-21    |
| 2       | logout   | 2019-06-21    |
| 3       | login    | 2019-01-01    |
| 3       | jobs     | 2019-01-01    |
| 3       | logout   | 2019-01-01    |
| 4       | login    | 2019-06-21    |
| 4       | groups   | 2019-06-21    |
| 4       | logout   | 2019-06-21    |
| 5       | login    | 2019-03-01    |
| 5       | logout   | 2019-03-01    |
| 5       | login    | 2019-06-21    |
| 5       | logout   | 2019-06-21    |
+---------+----------+---------------+

Result table:
+------------+-------------+
| login_date | user_count  |
+------------+-------------+
| 2019-05-01 | 1           |
| 2019-06-21 | 2           |
+------------+-------------+

Note that we only care about dates with non zero user count.

The user with id 5 first logged in on 2019-03-01 so he's not counted on 2019-06-21.


For each `user_id`, SELECT the minimum `activity_date` WHERE `activity = 'login'` AS `login_date`,
which is the first login date of the user.

Then use filters to only keep entries that are at most 90 days from 2019-06-30, and
COUNT the number of users for each `login_date`.
```
<br>

> The second solution is from [here](https://code.dennyzhang.com/new-users-daily-count)
<br>

```SQL
CREATE TABLE Traffic (user_id int, activity enum('login', 'logout', 'jobs', 'groups', 'homepage'), activity_date date);

INSERT INTO Traffic VALUES
(1, 'login', '2019-05-01'), (1, 'homepage', '2019-05-01'),
(1, 'logout', '2019-05-01'), (2, 'login', '2019-06-21'),
(2, 'logout', '2019-06-21'), (3, 'login', '2019-01-01'),
(3, 'jobs', '2019-01-01'), (3, 'logout', '2019-01-01'),
(4, 'login', '2019-06-21'), (4, 'groups', '2019-06-21'),
(4, 'logout', '2019-06-21'), (5, 'login', '2019-03-01'),
(5, 'logout', '2019-03-01'), (5, 'login', '2019-06-21'),
(5, 'logout', '2019-06-21');


SELECT login_date, COUNT(DISTINCT user_id) AS user_count
FROM (
    SELECT user_id, MIN(activity_date) AS login_date
    FROM Traffic
    WHERE activity = 'login'
    GROUP BY user_id
) first_login
WHERE DATEDIFF('2019-06-30', login_date) <= 90
GROUP BY login_date
ORDER BY login_date;


SELECT login_date, COUNT(1) AS user_count
FROM (
    SELECT user_id, MIN(activity_date) AS login_date
    FROM Traffic
    WHERE activity = 'login'
    GROUP BY user_id
) AS T
WHERE login_date >= DATE_ADD('2019-06-30', INTERVAL -90 DAY)
AND login_date <= '2019-06-30'
```