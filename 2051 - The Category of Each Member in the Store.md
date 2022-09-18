```
Table: Members

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| member_id   | int     |
| name        | varchar |
+-------------+---------+

member_id is the primary key column for this table.

Each row of this table indicates the name and the ID of a member.

 
Table: Visits

+-------------+------+
| Column Name | Type |
+-------------+------+
| visit_id    | int  |
| member_id   | int  |
| visit_date  | date |
+-------------+------+

visit_id is the primary key column for this table.

member_id is a foreign key to member_id from the Members table.

Each row of this table contains information about the date of a visit to the store and the member who visited it.

 
Table: Purchases

+----------------+------+
| Column Name    | Type |
+----------------+------+
| visit_id       | int  |
| charged_amount | int  |
+----------------+------+

visit_id is the primary key column for this table.

visit_id is a foreign key to visit_id from the Visits table.

Each row of this table contains information about the amount charged in a visit to the store.

 

A store wants to categorize its members. There are three tiers:

- "Diamond": if the conversion rate is greater than or equal to 80.
- "Gold": if the conversion rate is greater than or equal to 50 and less than 80.
- "Silver": if the conversion rate is less than 50.
- "Bronze": if the member never visited the store.

The conversion rate of a member is (100 * total number of purchases for the member) / total number of visits for the member.

Write a SQL query to report the id, the name, and the category of each member.

Return the result table in any order.

The query result format is in the following example.

Example 1:

Input: 
Members table:
+-----------+---------+
| member_id | name    |
+-----------+---------+
| 9         | Alice   |
| 11        | Bob     |
| 3         | Winston |
| 8         | Hercy   |
| 1         | Narihan |
+-----------+---------+

Visits table:
+----------+-----------+------------+
| visit_id | member_id | visit_date |
+----------+-----------+------------+
| 22       | 11        | 2021-10-28 |
| 16       | 11        | 2021-01-12 |
| 18       | 9         | 2021-12-10 |
| 19       | 3         | 2021-10-19 |
| 12       | 11        | 2021-03-01 |
| 17       | 8         | 2021-05-07 |
| 21       | 9         | 2021-05-12 |
+----------+-----------+------------+

Purchases table:
+----------+----------------+
| visit_id | charged_amount |
+----------+----------------+
| 12       | 2000           |
| 18       | 9000           |
| 17       | 7000           |
+----------+----------------+

Output: 
+-----------+---------+----------+
| member_id | name    | category |
+-----------+---------+----------+
| 1         | Narihan | Bronze   |
| 3         | Winston | Silver   |
| 8         | Hercy   | Diamond  |
| 9         | Alice   | Gold     |
| 11        | Bob     | Silver   |
+-----------+---------+----------+

Explanation: 
- User Narihan with id = 1 did not make any visits to the store. She gets a Bronze category.
- User Winston with id = 3 visited the store one time and did not purchase anything. The conversion rate = (100 * 0) / 1 = 0. He gets a Silver category.
- User Hercy with id = 8 visited the store one time and purchased one time. The conversion rate = (100 * 1) / 1 = 1. He gets a Diamond category.
- User Alice with id = 9 visited the store two times and purchased one time. The conversion rate = (100 * 1) / 2 = 50. She gets a Gold category.
- User Bob with id = 11 visited the store three times and purchased one time. The conversion rate = (100 * 1) / 3 = 33.33. He gets a Silver category.
```
<br>

> Solutions Links
<br>
> 1. [here](https://github.com/kamyu104/LeetCode-Solutions/blob/master/MySQL/the-category-of-each-member-in-the-store.sql)
> 2. [here](https://www.youtube.com/watch?v=B1A5-Og2WVc)
<br>

```SQL
CREATE TABLE Members (member_id int, name varchar(32), PRIMARY KEY(member_id));

CREATE TABLE Visits (visit_id int, member_id int, visit_date date, PRIMARY KEY (visit_id),
    FOREIGN KEY (member_id) REFERENCES Members (member_id));

CREATE TABLE Purchases (visit_id int, charged_amount int, PRIMARY KEY (visit_id),
    FOREIGN KEY (visit_id) REFERENCES Visits (visit_id));


INSERT INTO Members VALUES
(9, 'Alice'), (11, 'Bob'), (3, 'Winston'),
(8, 'Hercy'), (1, 'Narihan');

INSERT INTO Visits VALUES
(22, 11, '2021-10-28'), (16, 11, '2021-01-12'),
(18, 9, '2021-12-10'), (19, 3, '2021-10-19'),
(12, 11, '2021-03-01'), (17, 8, '2021-05-07'),
(21, 9, '2021-05-12');

INSERT INTO Purchases VALUES
(12, 2000), (18, 9000), (17, 7000);


WITH visits_cte AS (
    SELECT M.member_id, name, COUNT(visit_id) AS visit_cnt
    FROM Members M LEFT JOIN Visits V
    ON M.member_id = V.member_id
    GROUP BY M.member_id
    ORDER BY NULL
),
purchases_cte AS (
    SELECT member_id, COUNT(1) AS purchase_cnt
    FROM Purchases P INNER JOIN Visits V
    ON P.visit_id = V.visit_id
    GROUP BY member_id
    ORDER BY NULL
)
SELECT V.member_id, name,
CASE WHEN visit_cnt = 0 THEN 'Bronze'
WHEN IFNULL(purchase_cnt, 0) / visit_cnt < 0.5 THEN 'Silver'
WHEN IFNULL(purchase_cnt, 0) / visit_cnt < 0.8 THEN 'Gold'
ELSE 'Diamond'
END AS category
FROM visits_cte V LEFT JOIN purchases_cte P
ON V.member_id = P.member_id;


WITH cte AS (
    SELECT M.member_id, M.name, V.visit_id, P.charged_amount
    FROM Members M LEFT JOIN Visits V
    ON M.member_id = V.member_id
    LEFT JOIN Purchases P ON V.visit_id = P.visit_id
), cte2 AS (
    SELECT member_id, name,
           SUM(
               CASE
                   WHEN charged_amount IS NOT NULL THEN 1
                   ELSE 0
               END
           ) / COUNT(*) * 100 AS conversion_rate
    FROM cte
    GROUP BY member_id, name
)
SELECT member_id, name,
       CASE
           WHEN conversion_rate >= 80 THEN 'Diamond'
           WHEN conversion_rate >= 50 AND conversion_rate < 80 THEN 'Gold'
           WHEN conversion_rate < 50 AND member_id IN (
               SELECT DISTINCT member_id
               FROM Visits
           ) THEN 'Silver'
           ELSE 'Bronze'
           END AS category
FROM cte2;
```
***