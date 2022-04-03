```
Table: Friends

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| name          | varchar |
| activity      | varchar |
+---------------+---------+

id is the id of the friend and primary key for this table.

name is the name of the friend.

activity is the name of the activity which the friend takes part in.


Table: Activities

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| name          | varchar |
+---------------+---------+

id is the primary key for this table.

name is the name of the activity.


Write an SQL query to find the names of all the activities with neither maximum, nor minimum number of participants.

Return the result table in any order. Each activity in table Activities is performed by any person in the table Friends.

The query result format is in the following example:

Friends table:
+------+--------------+---------------+
| id   | name         | activity      |
+------+--------------+---------------+
| 1    | Jonathan D.  | Eating        |
| 2    | Jade W.      | Singing       |
| 3    | Victor J.    | Singing       |
| 4    | Elvis Q.     | Eating        |
| 5    | Daniel A.    | Eating        |
| 6    | Bob B.       | Horse Riding  |
+------+--------------+---------------+

Activities table:
+------+--------------+
| id   | name         |
+------+--------------+
| 1    | Eating       |
| 2    | Singing      |
| 3    | Horse Riding |
+------+--------------+

Result table:
+--------------+
| ACTIVITY     |
+--------------+
| Singing      |
+--------------+

Eating activity is performed by 3 friends, maximum number of participants, (Jonathan D. , Elvis Q. and Daniel A.)

Horse Riding activity is performed by 1 friend, minimum number of participants, (Bob B.)

Singing is performed by 2 friends (Victor J. and Jade W.)


The selection can be done only using table Friends without using table Activities.

Use ORDER BY and LIMIT 1 to select the minimum count (ascending) and the maximum count (descending).

Only select the activities with counts between minimum plus 1 and maximum minus 1.
```
<br>

```SQL
CREATE TABLE Friends (id int, name varchar(32), activity varchar(32));
CREATE TABLE Activities (id int, name varchar(32));

INSERT INTO Friends VALUES
(1, 'Jonathan D.', 'Eating'), (2, 'Jade W.', 'Singing'),
(3, 'Victor J.', 'Singing'), (4, 'Elvis Q.', 'Eating'),
(5, 'Daniel A.', 'Eating'), (6, 'Bob B.', 'Horse Riding');

INSERT INTO Activities VALUES
(1, 'Eating'), (2, 'Singing'), (3, 'Horse Riding');


SELECT A.activity AS activity
FROM (
    SELECT activity, COUNT(*)
    FROM Friends
    GROUP BY activity
    HAVING COUNT(*) BETWEEN
    (
        SELECT COUNT(*) C FROM Friends GROUP BY activity ORDER BY C ASC LIMIT 1
    ) + 1
    AND
    (
        SELECT COUNT(*) C FROM Friends GROUP BY activity ORDER BY C DESC LIMIT 1
    ) - 1
) A;


SELECT activity
FROM Friends
GROUP BY activity
HAVING COUNT(name) != (
    SELECT MAX(ma)
    FROM (
        SELECT activity, COUNT(name) AS ma
        FROM Friends
        GROUP BY activity
    ) max_act
) AND COUNT(name) != (
    SELECT MIN(mi)
    FROM (
        SELECT activity, COUNT(name) AS mi
        FROM Friends
        GROUP BY activity
    ) min_act
);
```
***