```
Table: UserVisits

+-------------+------+
| Column Name | Type |
+-------------+------+
| user_id     | int  |
| visit_date  | date |
+-------------+------+

This table does not have a primary key.

This table contains logs of the dates that users visited a certain retailer.

Assume today’s date is '2021-1-1'.


Write an SQL query that will, for each user_id, find out the largest window of days between each visit and the one right after it (or today if you are considering the last visit).

Return the result table ordered by user_id.

The query result format is in the following example:

UserVisits table:
+---------+------------+
| user_id | visit_date |
+---------+------------+
| 1       | 2020-11-28 |
| 1       | 2020-10-20 |
| 1       | 2020-12-3  |
| 2       | 2020-10-5  |
| 2       | 2020-12-9  |
| 3       | 2020-11-11 |
+---------+------------+

Result table:
+---------+---------------+
| user_id | biggest_window|
+---------+---------------+
| 1       | 39            |
| 2       | 65            |
| 3       | 51            |
+---------+---------------+


For the first user, the windows in question are between dates:
    - 2020-10-20 and 2020-11-28 with a total of 39 days. 
    - 2020-11-28 and 2020-12-3 with a total of 5 days. 
    - 2020-12-3 and 2021-1-1 with a total of 29 days.
Making the biggest window the one with 39 days.

For the second user, the windows in question are between dates:
    - 2020-10-5 and 2020-12-9 with a total of 65 days.
    - 2020-12-9 and 2021-1-1 with a total of 23 days.
Making the biggest window the one with 65 days.

For the third user, the only window in question is between dates 2020-11-11 and 2021-1-1 with a total of 51 days.


For each user, select the maximum difference between two adjacent visits’ dates. 

Let v1 and v2 be two entries of UserVisits such that v1.user_id = v2.user_id and v1.visit_date < v2.visit_date. 

For each v1, find the v2 with the smallest visit_date and calculate the maximum date difference. 

Finally, group the result by user_id.
```
<br>

```SQL
CREATE TABLE UserVisits (user_id int, visit_date date);

INSERT INTO UserVisits VALUES
(1, '2020-11-28'), (1, '2020-10-20'), (1, '2020-12-3'),
(2, '2020-10-5'), (2, '2020-12-9'), (3, '2020-11-11');


SELECT user_id, MAX(diff) AS biggest_window
FROM (
    SELECT v1.user_id, DATEDIFF(IFNULL(MIN(v2.visit_date), '2021-1-1'), v1.visit_date) AS diff
    FROM UserVisits v1 LEFT JOIN UserVisits v2
    ON v1.user_id = v2.user_id AND v1.visit_date < v2.visit_date
    GROUP BY v1.user_id, v1.visit_date
) AS temp
GROUP BY user_id
ORDER BY user_id;


SELECT user_id, MAX(diff) AS biggest_window
FROM (
    SELECT user_id, DATEDIFF(COALESCE(LEAD(visit_date) OVER (PARTITION BY user_id ORDER BY visit_date), '2021-01-01'), visit_date) AS diff
    FROM UserVisits
) T
GROUP BY user_id
ORDER BY user_id;
```
***