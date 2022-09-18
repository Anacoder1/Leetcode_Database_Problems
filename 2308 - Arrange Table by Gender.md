```
Table: Genders

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| user_id     | int     |
| gender      | varchar |
+-------------+---------+

user_id is the primary key for this table.

gender is ENUM of type 'female', 'male', or 'other'.

Each row in this table contains the ID of a user and their gender.

The table has an equal number of 'female', 'male', and 'other'.


Write a SQL query to rearrange the Genders table such that the rows alternate between 'female', 'other', and 'male' in order.

The table should be rearranged such that the IDs of each gender are sorted in ascending order.

Return the result table in the mentioned order.

The query result format is shown in the following example.

Example 1:

Input: 
Genders table:
+---------+--------+
| user_id | gender |
+---------+--------+
| 4       | male   |
| 7       | female |
| 2       | other  |
| 5       | male   |
| 3       | female |
| 8       | male   |
| 6       | other  |
| 1       | other  |
| 9       | female |
+---------+--------+

Output: 
+---------+--------+
| user_id | gender |
+---------+--------+
| 3       | female |
| 1       | other  |
| 4       | male   |
| 7       | female |
| 2       | other  |
| 5       | male   |
| 9       | female |
| 6       | other  |
| 8       | male   |
+---------+--------+

Explanation: 
Female gender: IDs 3, 7, and 9.
Other gender: IDs 1, 2, and 6.
Male gender: IDs 4, 5, and 8.
We arrange the table alternating between 'female', 'other', and 'male'.
Note that the IDs of each gender are sorted in ascending order.
```
<br>

> Solutions Links
<br>
> 1. [here](https://github.com/kamyu104/LeetCode-Solutions/blob/master/MySQL/arrange-table-by-gender.sql)
> 2. [here](https://velog.io/@dev_taehyun/algorithm-leetcode-2308)
<br>

```SQL
CREATE TABLE Genders (user_id int, gender enum('female', 'male', 'other'), PRIMARY KEY (user_id));

INSERT INTO Genders VALUES
(4, 'male'), (7, 'female'), (2, 'other'),
(5, 'male'), (3, 'female'), (8, 'male'),
(6, 'other'), (1, 'other'), (9, 'female');


WITH genders_cte AS (
    SELECT *, RANK() OVER(PARTITION BY gender ORDER BY user_id) AS rank_1,
          CASE gender
              WHEN 'female' THEN 1
              WHEN 'other' THEN 2
              ELSE 3
          END AS rank_2
    FROM Genders
    ORDER BY NULL
)
SELECT user_id, gender
FROM genders_cte
ORDER BY rank_1, rank_2;


SELECT user_id, gender
FROM (
    SELECT user_id, gender,
           ROW_NUMBER() OVER(PARTITION BY gender ORDER BY user_id ASC) AS id_order,
           CASE gender
               WHEN 'female' THEN 1
               WHEN 'other' THEN 2
               ELSE 3
           END AS gender_order
    FROM Genders
) AS OrderedGenders
ORDER BY id_order ASC, gender_order ASC;
```
***