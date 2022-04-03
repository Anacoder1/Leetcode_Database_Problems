```
(couldn't find the complete question anywhere)
```
<br>

> Solution taken from [here](https://github.com/kamyu104/LeetCode-Solutions/blob/master/MySQL/accepted-candidates-from-the-interviews.sql)
<br>

```SQL
SELECT candidate_id
FROM Candidates AS C INNER JOIN Rounds AS R
ON C.interview_id = R.interview_id
WHERE years_of_exp >= 2
GROUP BY candidate_id
HAVING SUM(score) > 15
ORDER BY NULL;
```
***