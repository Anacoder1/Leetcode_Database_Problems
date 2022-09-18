```
Table: Logs

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| num         | varchar |
+-------------+---------+

id is the primary key for this table.

id is an autoincrement column.


Write a SQL query to find all numbers that appear at least three times consecutively.

Return the result table in any order.

The query result format is in the following example.


Example 1:

Input: 
Logs table:
+----+-----+
| id | num |
+----+-----+
| 1  | 1   |
| 2  | 1   |
| 3  | 1   |
| 4  | 2   |
| 5  | 1   |
| 6  | 2   |
| 7  | 2   |
+----+-----+

Output: 
+-----------------+
| ConsecutiveNums |
+-----------------+
| 1               |
+-----------------+

Explanation: 1 is the only number that appears consecutively for at least three times.


Find directly in the three instances of the table, and then limit on the four conditions.
```
<br>

```SQL
CREATE TABLE Logs (id int, num int, PRIMARY KEY (id));

INSERT INTO Logs VALUES
(1, 1), (2, 1), (3, 1), (4, 2), (5, 1), (6, 2), (7, 2);


SELECT DISTINCT L1.Num AS ConsecutiveNums
FROM Logs L1, Logs L2, Logs L3
WHERE L1.Id = L2.Id - 1 
    AND L2.Id = L3.Id - 1
    AND L1.Num = L2.Num
    AND L2.Num = L3.Num;
    
    
[here](https://circlecoder.com/consecutive-numbers/)
SELECT L1.num AS ConsecutiveNums
FROM Logs L1, Logs L2, Logs L3
WHERE L1.num = L2.num 
AND L2.num = L3.num
AND L1.id = L2.id + 1
AND L2.id = L3.id + 1
GROUP BY L1.num;


[here](https://leetcode.com/problems/consecutive-numbers/solution/192372)
SELECT DISTINCT num AS ConsecutiveNums
FROM (
    SELECT num, 
          LEAD(num) OVER(ORDER BY id) AS `lead`,
          LAG(num) OVER(ORDER BY id) AS `lag`
    FROM Logs
) t
WHERE num = `lead` and num = `lag`;


[here](https://leetcode.com/problems/consecutive-numbers/discuss/321205/Runtime:-299-ms-faster-than-94.39)
SELECT DISTINCT num AS ConsecutiveNums
FROM Logs
WHERE (id + 1, num) IN (SELECT * FROM Logs)
AND (id + 2, num) IN (SELECT * FROM Logs);


[here](https://leetcode.com/problems/consecutive-numbers/discuss/237228/My-version-is-more-flexible.-We-can-change-3-to-4-or-5-or-10)
SELECT DISTINCT num AS ConsecutiveNums
FROM (
    SELECT num, SUM(c) OVER (ORDER BY id) AS flag
    FROM (
        SELECT id, num,
               CASE
                   WHEN LAG(num) OVER (ORDER BY id) - num = 0 THEN 0 ELSE 1
               END AS c
        FROM Logs
    ) A
) B
GROUP BY num, flag
HAVING COUNT(*) >= 3; -- (3 can be changed to any number)


[here](https://leetcode.com/problems/consecutive-numbers/discuss/53420/Solution-with-user-defined-variables)
SELECT DISTINCT num AS ConsecutiveNums
FROM (
    SELECT num,
           CASE
               WHEN @record = num THEN @count := @count + 1
               WHEN @record <> @record := num THEN @count := 1
           END AS n
    FROM Logs, (
        SELECT @count := 0, @record := (SELECT num FROM Logs LIMIT 0, 1)
    ) R
) A
WHERE A.n >= 3;


[here](https://leetcode.com/problems/consecutive-numbers/discuss/53420/Solution-with-user-defined-variables/118061)
SELECT DISTINCT num AS ConsecutiveNums
FROM (
    SELECT num,
           CASE
               WHEN @record = num THEN @count := @count + 1
               WHEN @record := num THEN @count := 1
           END AS n
    FROM Logs, (SELECT @count := 0, @record := NULL) R
) A
WHERE A.n >= 3;
```
***