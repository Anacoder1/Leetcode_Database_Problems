```
Table: Contests

+--------------+------+
| Column Name  | Type |
+--------------+------+
| contest_id   | int  |
| gold_medal   | int  |
| silver_medal | int  |
| bronze_medal | int  |
+--------------+------+

contest_id is the primary key for this table.

This table contains the LeetCode contest ID and the user IDs of the gold, silver, and bronze medalists.

It is guaranteed that any consecutive contests have consecutive IDs and that no ID is skipped.


Table: Users

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| user_id     | int     |
| mail        | varchar |
| name        | varchar |
+-------------+---------+

user_id is the primary key for this table.

This table contains information about the users.


Write an SQL query to report the name and the mail of all interview candidates.

A user is an interview candidate if at least one of these two conditions is true:

- The user won any medal in three or more consecutive contests.
- The user won the gold medal in three or more different contests (not necessarily consecutive).

Return the result table in any order.

The query result format is in the following example:

Contests table:
+------------+------------+--------------+--------------+
| contest_id | gold_medal | silver_medal | bronze_medal |
+------------+------------+--------------+--------------+
| 190        | 1          | 5            | 2            |
| 191        | 2          | 3            | 5            |
| 192        | 5          | 2            | 3            |
| 193        | 1          | 3            | 5            |
| 194        | 4          | 5            | 2            |
| 195        | 4          | 2            | 1            |
| 196        | 1          | 5            | 2            |
+------------+------------+--------------+--------------+

Users table:
+---------+--------------------+-------+
| user_id | mail               | name  |
+---------+--------------------+-------+
| 1       | sarah@leetcode.com | Sarah |
| 2       | bob@leetcode.com   | Bob   |
| 3       | alice@leetcode.com | Alice |
| 4       | hercy@leetcode.com | Hercy |
| 5       | quarz@leetcode.com | Quarz |
+---------+--------------------+-------+

Result table:
+-------+--------------------+
| name  | mail               |
+-------+--------------------+
| Sarah | sarah@leetcode.com |
| Bob   | bob@leetcode.com   |
| Alice | alice@leetcode.com |
| Quarz | quarz@leetcode.com |
+-------+--------------------+


Sarah won 3 gold medals (190, 193, and 196), so we include her in the result table.

Bob won a medal in 3 consecutive contests (190, 191, and 192), so we include him in the result table.
- Note that he also won a medal in 3 other consecutive contests (194, 195, and 196).

Alice won a medal in 3 consecutive contests (191, 192, and 193), so we include her in the result table.

Quarz won a medal in 5 consecutive contests (190, 191, 192, 193, and 194), so we include them in the result table.


FOLLOW UP:
- What if the first condition changed to be “any medal in n or more consecutive contests”? How would you change your solution to get the interview candidates? Imagine that n is the parameter of a stored procedure.

- Suppose we tracked which contests each user participated in. What if we wanted to consider candidates that won any medal in three or more consecutive contests where they were a participant? How would this change your solution?


For the first condition, select the users with user_id that occur in three consecutive entries of Contests table. 

For the second condition, select the users with user_id that occur in column gold_medal in at least three entries of Contests table.
```
<br>

```SQL
CREATE TABLE Contests (contest_id int, gold_medal int, silver_medal int, bronze_medal int);
CREATE TABLE Users (user_id int, mail varchar(32), name varchar(32));

INSERT INTO Contests VALUES
(190, 1, 5, 2), (191, 2, 3, 5), (192, 5, 2, 3),
(193, 1, 3, 5), (194, 4, 5, 2), (195, 4, 2, 1),
(196, 1, 5, 2);

INSERT INTO Users VALUES
(1, 'sarah@leetcode.com', 'Sarah'), (2, 'bob@leetcode.com', 'Bob'),
(3, 'alice@leetcode.com', 'Alice'), (4, 'hercy@leetcode.com', 'Hercy'),
(5, 'quarz@leetcode.com', 'Quarz');


SELECT U.name, U.mail
FROM Users U WHERE (
    SELECT COUNT(C1.contest_id)
    FROM Contests C1, Contests C2, Contests C3
    WHERE C2.contest_id - C1.contest_id = 1 AND C3.contest_id - C2.contest_id = 1
    AND (
        C1.gold_medal = U.user_id OR C1.silver_medal = U.user_id OR C1.bronze_medal = U.user_id
    ) AND (
        C2.gold_medal = U.user_id OR C2.silver_medal = U.user_id OR C2.bronze_medal = U.user_id
    ) AND (
        C3.gold_medal = U.user_id OR C3.silver_medal = U.user_id OR C3.bronze_medal = U.user_id
    )
) >= 1
OR (
    SELECT COUNT(*)
    FROM Contests C
    WHERE C.gold_medal = U.user_id
) >= 3;


SELECT name, mail
FROM Users
WHERE user_id IN (
    SELECT gold_medal AS user_id
    FROM Contests
    GROUP BY gold_medal
    HAVING COUNT(gold_medal) >= 3
    UNION ALL (
        WITH contest_and_medal AS (
            SELECT contest_id, gold_medal AS user_id
            FROM Contests
            UNION ALL
            SELECT contest_id, silver_medal AS user_id
            FROM Contests
            UNION ALL
            SELECT contest_id, bronze_medal AS user_id
            FROM Contests
        )
        SELECT DISTINCT C1.user_id
        FROM contest_and_medal C1, contest_and_medal C2, contest_and_medal C3
        WHERE C1.contest_id - C2.contest_id = 1 AND C2.contest_id - C3.contest_id = 1
        AND C1.user_id = C2.user_id AND C1.user_id = C3.user_id
    )
);
```
***