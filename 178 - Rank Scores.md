```
Write a SQL query to rank scores.

If there is a tie between two scores, both should have the same ranking.

Note that after a tie, the next ranking number should be the next consecutive integer value.

In other words, there should be no "holes" between ranks.

+----+-------+
| Id | Score |
+----+-------+
| 1  | 3.50  |
| 2  | 3.65  |
| 3  | 4.00  |
| 4  | 3.85  |
| 5  | 4.00  |
| 6  | 3.65  |
+----+-------+


For example, given the above Scores table, your query should generate the following report (order by highest score):

+-------+------+
| Score | Rank |
+-------+------+
| 4.00  | 1    |
| 4.00  | 1    |
| 3.85  | 2    |
| 3.65  | 3    |
| 3.65  | 3    |
| 3.50  | 4    |
+-------+------+


For each score, find out how many different scores are greater than or equal to the score in the table, and then sort them in descending order.

Or, JOIN (short form of Inner Join), the condition is that the score of the right table is greater than or equal to the left table, and then the groups are ranked in descending order of the scores.
```
<br>

```SQL
CREATE TABLE Scores (Id int, Score decimal(3, 2));

INSERT INTO Scores VALUES
(1, 3.50), (2, 3.65), (3, 4.00),
(4, 3.85), (5, 4.00), (6, 3.65);


SELECT Score,
(
    SELECT COUNT(DISTINCT Score)
    FROM Scores
    WHERE Score >= S.Score
) AS `Rank`
FROM Scores S
ORDER BY Score DESC;


SELECT Score,
(
    SELECT COUNT(DISTINCT Score) + 1
    FROM Scores SS
    WHERE SS.Score > S.score
) `Rank`
FROM Scores S
ORDER BY Score DESC;
```
***