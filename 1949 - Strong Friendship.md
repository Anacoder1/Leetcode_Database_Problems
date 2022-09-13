```
Table: Friendship

+-------------+------+
| Column Name | Type |
+-------------+------+
| user1_id    | int  |
| user2_id    | int  |
+-------------+------+

(user1_id, user2_id) is the primary key for this table.

Each row of this table indicates that the users user1_id and user2_id are friends.

Note that user1_id < user2_id.

A friendship between a pair of friends x and y is strong if x and y have at least three common friends.


Write an SQL query to find all the strong friendships.

Note that the result table should not contain duplicates with user1_id < user2_id.

Return the result table in any order.

The query result format is in the following example:

Friendship table:
+----------+----------+
| user1_id | user2_id |
+----------+----------+
| 1        | 2        |
| 1        | 3        |
| 2        | 3        |
| 1        | 4        |
| 2        | 4        |
| 1        | 5        |
| 2        | 5        |
| 1        | 7        |
| 3        | 7        |
| 1        | 6        |
| 3        | 6        |
| 2        | 6        |
+----------+----------+

Result table:
+----------+----------+---------------+
| user1_id | user2_id | common_friend |
+----------+----------+---------------+
| 1        | 2        | 4             |
| 1        | 3        | 3             |
+----------+----------+---------------+

Users 1 and 2 have 4 common friends (3, 4, 5, and 6).

Users 1 and 3 have 3 common friends (2, 6, and 7).

We did not include the friendship of users 2 and 3 because they only have two common friends (1 and 6).


Use UNION ALL to generate the table.

Use JOIN and GROUP BY to filter the entries that satisfy all requirements.
```
<br>

```SQL
CREATE TABLE Friendship (user1_id int, user2_id int, PRIMARY KEY (user1_id, user2_id));

INSERT INTO Friendship VALUES
(1, 2), (1, 3), (2, 3), (1, 4), (2, 4),
(1, 5), (2, 5), (1, 7), (3, 7), (1, 6),
(3, 6), (2, 6);


WITH T AS (
    SELECT * FROM Friendship
    UNION ALL
    SELECT user2_id, user1_id FROM Friendship
    ORDER BY user1_id, user2_id
)
SELECT DISTINCT *
FROM (
    SELECT T1.user1_id, T2.user1_id AS user2_id, COUNT(T2.user1_id) AS common_friend
    FROM T AS T1 JOIN T AS T2
    ON T1.user1_id != T2.user1_id AND T1.user2_id = T2.user2_id
    GROUP BY T1.user1_id, T2.user1_id
    HAVING COUNT(T2.user1_id) >= 3
) AS XYZ
WHERE (user1_id, user2_id) IN (SELECT * FROM Friendship);


WITH friendtable AS (
    SELECT user1_id AS userid, user2_id AS friend
    FROM Friendship
    UNION
    SELECT user2_id AS userid, user1_id AS friend
    FROM Friendship
)
SELECT F1.userid AS user1_id, F2.userid AS user2_id, COUNT(F1.friend) AS common_friend
FROM friendtable AS F1 JOIN friendtable AS F2
ON F1.friend = F2.friend
WHERE F1.userid < F2.userid
AND (F1.userid, F2.userid) IN (
    SELECT user1_id, user2_id
    FROM Friendship
)
GROUP BY F1.userid, F2.userid
HAVING COUNT(F2.friend) >= 3;
```
***