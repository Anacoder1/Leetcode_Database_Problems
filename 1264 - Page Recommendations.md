```
Table: Friendship

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| user1_id      | int     |
| user2_id      | int     |
+---------------+---------+

(user1_id, user2_id) is the primary key for this table.

Each row of this table indicates that there is a friendship relation between user1_id and user2_id.


Table: Likes

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| user_id     | int     |
| page_id     | int     |
+-------------+---------+

(user_id, page_id) is the primary key for this table.

Each row of this table indicates that user_id likes page_id.


Write an SQL query to recommend pages to the user with user_id = 1 using the pages that your friends liked.

It should not recommend pages you already liked.

Return result table in any order without duplicates.

The query result format is in the following example:

Friendship table:
+----------+----------+
| user1_id | user2_id |
+----------+----------+
| 1        | 2        |
| 1        | 3        |
| 1        | 4        |
| 2        | 3        |
| 2        | 4        |
| 2        | 5        |
| 6        | 1        |
+----------+----------+
 
Likes table:
+---------+---------+
| user_id | page_id |
+---------+---------+
| 1       | 88      |
| 2       | 23      |
| 3       | 24      |
| 4       | 56      |
| 5       | 11      |
| 6       | 33      |
| 2       | 77      |
| 3       | 77      |
| 6       | 88      |
+---------+---------+

Result table:
+------------------+
| recommended_page |
+------------------+
| 23               |
| 24               |
| 56               |
| 33               |
| 77               |
+------------------+

User one is friend with users 2, 3, 4 and 6.

Suggested pages are 23 from user 2, 24 from user 3, 56 from user 4 and 33 from user 6.

Page 77 is suggested from both user 2 and user 3.

Page 88 is not suggested because user 1 already likes it.


To find friends of the user with user_id = 1, use user1_id UNION ALL user2_id in table Friendship.

Then use SELECT DISTINCT to select all pages that the friends liked.

To remove pages that the user with user_id = 1 already liked, use NOT IN to eliminate such entries.
```
<br>

```SQL
CREATE TABLE Friendship (user1_id int, user2_id int);
CREATE TABLE Likes (user_id int, page_id int);

INSERT INTO Friendship VALUES
(1, 2), (1, 3), (1, 4), (2, 3), (2, 4),
(2, 5), (6, 1);

INSERT INTO Likes VALUES
(1, 88), (2, 23), (3, 24), (4, 56),
(5, 11), (6, 33), (2, 77), (3, 77),
(6, 88);


SELECT DISTINCT page_id AS recommended_page
FROM Likes
WHERE user_id IN (
    SELECT user1_id FROM Friendship WHERE user2_id = 1
    UNION ALL
    SELECT user2_id FROM Friendship WHERE user1_id = 1
) AND page_id NOT IN (
    SELECT page_id
    FROM Likes
    WHERE user_id = 1
);


SELECT DISTINCT L.page_id AS recommended_page
FROM Likes L
LEFT JOIN Friendship F1 ON L.user_id = F1.user2_id
LEFT JOIN Friendship F2 ON L.user_id = F2.user1_id
WHERE (F1.user1_id = 1 OR F2.user2_id = 1) AND
L.page_id NOT IN (
    SELECT page_id
    FROM Likes
    WHERE user_id = 1
);
```
***