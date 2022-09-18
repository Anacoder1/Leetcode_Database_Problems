```
Table: Actions

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| user_id       | int     |
| post_id       | int     |
| action_date   | date    | 
| action        | enum    |
| extra         | varchar |
+---------------+---------+

There is no primary key for this table, it may have duplicate rows.

The action column is an ENUM type of ('view', 'like', 'reaction', 'comment', 'report', 'share').

The extra column has optional information about the action such as a reason for report or a type of reaction. 


Table: Removals

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| post_id       | int     |
| remove_date   | date    | 
+---------------+---------+

post_id is the primary key of this table.

Each row in this table indicates that some post was removed as a result of being reported or as a result of an admin review.


Write a SQL query to find the average for daily percentage of posts that got removed after being reported as spam, rounded to 2 decimal places.

The query result format is in the following example:

Actions table:
+---------+---------+-------------+--------+--------+
| user_id | post_id | action_date | action | extra  |
+---------+---------+-------------+--------+--------+
| 1       | 1       | 2019-07-01  | view   | null   |
| 1       | 1       | 2019-07-01  | like   | null   |
| 1       | 1       | 2019-07-01  | share  | null   |
| 2       | 2       | 2019-07-04  | view   | null   |
| 2       | 2       | 2019-07-04  | report | spam   |
| 3       | 4       | 2019-07-04  | view   | null   |
| 3       | 4       | 2019-07-04  | report | spam   |
| 4       | 3       | 2019-07-02  | view   | null   |
| 4       | 3       | 2019-07-02  | report | spam   |
| 5       | 2       | 2019-07-03  | view   | null   |
| 5       | 2       | 2019-07-03  | report | racism |
| 5       | 5       | 2019-07-03  | view   | null   |
| 5       | 5       | 2019-07-03  | report | racism |
+---------+---------+-------------+--------+--------+

Removals table:
+---------+-------------+
| post_id | remove_date |
+---------+-------------+
| 2       | 2019-07-20  |
| 3       | 2019-07-18  |
+---------+-------------+

Result table:
+-----------------------+
| average_daily_percent |
+-----------------------+
| 75.00                 |
+-----------------------+

The percentage for 2019-07-04 is 50% because only one post of two spam reported posts was removed.

The percentage for 2019-07-02 is 100% because one post was reported as spam and it was removed.

The other days had no spam reports, so the average is (50 + 100) / 2 = 75%.

NOTE that the output is only one number and that we do not care about the remove dates.


To find the average, use IFNULL.
To round the result to 2 decimal places, use ROUND.
Join the tables `Actions` and `Removals`.
Count the no. of posts being reported as spam from table `Actions`,
where the `extra` column is 'spam'.

Count the no. of removed posts from table `Removals`.
```
<br>

```SQL
CREATE TABLE Actions (user_id int, post_id int, action_date date, action enum('view', 'like', 'reaction', 'comment', 'report', 'share'),
    extra varchar(32));
    
CREATE TABLE Removals (post_id int, remove_date date, PRIMARY KEY (post_id));


INSERT INTO Actions VALUES
(1, 1, '2019-07-01', 'view', null),
(1, 1, '2019-07-01', 'like', null),
(1, 1, '2019-07-01', 'share', null),
(2, 2, '2019-07-04', 'view', null),
(2, 2, '2019-07-04', 'report', 'spam'),
(3, 4, '2019-07-04', 'view', null),
(3, 4, '2019-07-04', 'report', 'spam'),
(4, 3, '2019-07-02', 'view', null),
(4, 3, '2019-07-02', 'report', 'spam'),
(5, 2, '2019-07-03', 'view', null),
(5, 2, '2019-07-03', 'report', 'racism'),
(5, 5, '2019-07-03', 'view', null),
(5, 5, '2019-07-03', 'report', 'racism');

INSERT INTO Removals VALUES
(2, '2019-07-20'), (3, '2019-07-18');


SELECT IFNULL(
    ROUND(
        SUM(remove_count / spam_count * 100) / COUNT(DISTINCT action_date), 2
    ), 0
) AS average_daily_percent
FROM (
    SELECT action_date, COUNT(DISTINCT A.post_id) AS spam_count,
          COUNT(DISTINCT R.post_id) AS remove_count
    FROM Actions A LEFT JOIN Removals R
    ON A.post_id = R.post_id
    WHERE A.extra = 'spam'
    GROUP BY A.action_date
) AS X;


SELECT ROUND(AVG(daily_count), 2) AS average_daily_percent
FROM (
    SELECT COUNT(DISTINCT B.post_id) / COUNT(DISTINCT A.post_id) * 100 AS daily_count
    FROM Actions A LEFT JOIN Removals B
    ON A.post_id = B.post_id
    WHERE extra = 'spam'
    GROUP BY action_date
) X;
```
***