```
Table: Candidate

+-----+---------+
| id  | Name    |
+-----+---------+
| 1   | A       |
| 2   | B       |
| 3   | C       |
| 4   | D       |
| 5   | E       |
+-----+---------+


Table: Vote

+-----+--------------+
| id  | CandidateId  |
+-----+--------------+
| 1   |     2        |
| 2   |     4        |
| 3   |     3        |
| 4   |     2        |
| 5   |     5        |
+-----+--------------+

id is the auto-increment primary key,

CandidateId is the id appeared in Candidate table.


Write a SQL query to find the name of the winning candidate, the above example will return the winner B.

+------+
| Name |
+------+
| B    |
+------+


NOTE : You may assume there is no tie, in other words there will be only one winning candidate.


First, SELECT the CandidateId that occurs the most in the Vote table.

Next, use the CandidateId as Id to find the candidate's name from the Candidate table.
```
<br>

```SQL
CREATE TABLE Candidate (id int, Name varchar(32), PRIMARY KEY(id));
CREATE TABLE Vote (id int, CandidateId int, FOREIGN KEY(CandidateId) REFERENCES Candidate(id));

INSERT INTO Candidate VALUES
(1, 'A'), (2, 'B'), (3, 'C'), (4, 'D'), (5, 'E');

INSERT INTO Vote VALUES
(1, 2), (2, 4), (3, 3), (4, 2), (5, 5);


SELECT Name
FROM Candidate
WHERE id IN (
    SELECT CandidateId
    FROM (
        SELECT CandidateId, COUNT(*) AS counts
        FROM Vote
        GROUP BY CandidateId
        ORDER BY counts DESC
        LIMIT 1
    ) AS Winner
);


SELECT Name
FROM Candidate
JOIN (
    SELECT CandidateId
    FROM Vote
    GROUP BY CandidateId
    ORDER BY COUNT(*) DESC
    LIMIT 1
) AS Winner
WHERE Candidate.id = Winner.CandidateId;
```
***