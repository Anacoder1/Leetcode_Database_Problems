```
Table: Teams

+---------------+----------+
| Column Name   | Type     |
+---------------+----------+
| team_id       | int      |
| team_name     | varchar  |
+---------------+----------+

team_id is the primary key of this table.

Each row of this table represents a single football team.


Table: Matches

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| match_id      | int     |
| host_team     | int     |
| guest_team    | int     | 
| host_goals    | int     |
| guest_goals   | int     |
+---------------+---------+

match_id is the primary key of this table.

Each row is a record of a finished match between two different teams. 

Teams host_team and guest_team are represented by their IDs in the teams table (team_id) and they scored host_goals and guest_goals goals respectively.


You would like to compute the scores of all teams after all matches. Points are awarded as follows:

* A team receives three points if they win a match (Score strictly more goals than the opponent team).
* A team receives one point if they draw a match (Same number of goals as the opponent team).
* A team receives no points if they lose a match (Score less goals than the opponent team).


Write a SQL query that selects the team_id, team_name and num_points of each team in the tournament after all described matches.

Result table should be ordered by num_points (decreasing order).

In case of a tie, order the records by team_id (increasing order).


The query result format is in the following example:

Teams table:
+-----------+--------------+
| team_id   | team_name    |
+-----------+--------------+
| 10        | Leetcode FC  |
| 20        | NewYork FC   |
| 30        | Atlanta FC   |
| 40        | Chicago FC   |
| 50        | Toronto FC   |
+-----------+--------------+

Matches table:
+------------+--------------+---------------+-------------+--------------+
| match_id   | host_team    | guest_team    | host_goals  | guest_goals  |
+------------+--------------+---------------+-------------+--------------+
| 1          | 10           | 20            | 3           | 0            |
| 2          | 30           | 10            | 2           | 2            |
| 3          | 10           | 50            | 5           | 1            |
| 4          | 20           | 30            | 1           | 0            |
| 5          | 50           | 30            | 1           | 0            |
+------------+--------------+---------------+-------------+--------------+

Result table:
+------------+--------------+---------------+
| team_id    | team_name    | num_points    |
+------------+--------------+---------------+
| 10         | Leetcode FC  | 7             |
| 20         | NewYork FC   | 3             |
| 50         | Toronto FC   | 3             |
| 30         | Atlanta FC   | 1             |
| 40         | Chicago FC   | 0             |
+------------+--------------+---------------+


For each team in table `Teams`, obtain the entries from table `Matches` and calculate the scores accordingly.

If an entry in table `Matches` have the current team as `host_team` or `guest_team`, then the current team may get 3 points, 1 point or no points.

Otherwise, the team has no points for the entry.

Finally, sort the results by num_points in descending order and then by team_id in ascending order.
```
<br>

```SQL
CREATE TABLE Teams (team_id int, team_name varchar(32), PRIMARY KEY (team_id));
CREATE TABLE Matches (match_id int, host_team int, guest_team int, host_goals int, guest_goals int, 
FOREIGN KEY (host_team) REFERENCES Teams(team_id),
FOREIGN KEY (guest_team) REFERENCES Teams(team_id));
    
INSERT INTO Teams VALUES
(10, 'Leetcode FC'), (20, 'NewYork FC'), (30, 'Atlanta FC'),
(40, 'Chicago FC'), (50, 'Toronto FC');

INSERT INTO Matches VALUES
(1, 10, 20, 3, 0), (2, 30, 10, 2, 2), (3, 10, 50, 5, 1),
(4, 20, 30, 1, 0), (5, 50, 30, 1, 0);


SELECT team_id, team_name, num_points
FROM (
    SELECT Teams.team_id, Teams.team_name, SUM(
        CASE
            WHEN Teams.team_id = Matches.host_team THEN
            CASE
                WHEN Matches.host_goals > Matches.guest_goals THEN 3
                WHEN Matches.host_goals = Matches.guest_goals THEN 1
                ELSE 0
            END
            WHEN Teams.team_id = Matches.guest_team THEN
            CASE
                WHEN Matches.host_goals < Matches.guest_goals THEN 3
                WHEN Matches.host_goals = Matches.guest_goals THEN 1
                ELSE 0
            END
            ELSE 0
        END
    ) AS num_points
    FROM Teams LEFT JOIN Matches
    ON Teams.team_id = Matches.host_team OR Teams.team_id = Matches.guest_team
    GROUP BY Teams.team_id
) result
ORDER BY result.num_points DESC, result.team_id;


SELECT T.team_id, T.team_name,
IFNULL(
    SUM(
        CASE
            WHEN T.team_id = M.host_team AND M.host_goals > M.guest_goals THEN 3
            WHEN T.team_id = M.host_team AND M.host_goals = M.guest_goals THEN 1
            WHEN T.team_id = M.guest_team AND M.host_goals < M.guest_goals THEN 3
            WHEN T.team_id = M.guest_team AND M.host_goals = M.guest_goals THEN 1
            ELSE 0
        END
    ), 0
) AS num_points
FROM Matches M RIGHT JOIN Teams T
ON M.host_team = T.team_id OR M.guest_team = T.team_id
GROUP BY team_id, team_name
ORDER BY num_points DESC, team_id;
```
***