```
(couldn't find complete question anywhere)
```
<br>

> Solution taken from [here](https://github.com/kamyu104/LeetCode-Solutions/blob/master/MySQL/the-category-of-each-member-in-the-store.sql)
<br>

```SQL
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
```
***