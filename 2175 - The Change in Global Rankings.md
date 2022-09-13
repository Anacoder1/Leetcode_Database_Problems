```
(couldn't find the question description)
```
<br>

> Solution taken from [here](https://github.com/kamyu104/LeetCode-Solutions/blob/master/MySQL/the-change-in-global-rankings.sql)
<br>

```SQL
SELECT A.team_id, A.name,
CAST(ROW_NUMBER() OVER (ORDER BY points DESC, name ASC) AS SIGNED) -
CAST(ROW_NUMBER() OVER (ORDER BY points + points_change DESC, name ASC) AS SIGNED) AS rank_diff
FROM TeamPoints AS A INNER JOIN PointsChange AS B
ON A.team_id = B.team_id;
```
***