```
Table: Activity

+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| player_id    | int     |
| device_id    | int     |
| event_date   | date    |
| games_played | int     |
+--------------+---------+

(player_id, event_date) is the primary key of this table.

This table shows the activity of players of some game.

Each row is a record of a player who logged in and played a number of games (possibly 0) before logging out on some day using some device.


Write a SQL query that reports the fraction of players that logged in again on the day after the day they first logged in, rounded to 2 decimal places.

In other words, you need to count the number of players that logged in for at least two consecutive days starting from their first login date,
then divide that number by the total number of players.

The query result format is in the following example:

Activity table:
+-----------+-----------+------------+--------------+
| player_id | device_id | event_date | games_played |
+-----------+-----------+------------+--------------+
| 1         | 2         | 2016-03-01 | 5            |
| 1         | 2         | 2016-03-02 | 6            |
| 2         | 3         | 2017-06-25 | 1            |
| 3         | 1         | 2016-03-02 | 0            |
| 3         | 4         | 2018-07-03 | 5            |
+-----------+-----------+------------+--------------+

Result table:
+-----------+
| fraction  |
+-----------+
| 0.33      |
+-----------+

Only the player with id 1 logged back in after the first day he had logged in so the answer is 1/3 = 0.33


[For First Solution]
To obtain a fraction, both the numerator and denominator need to be obtained.

To obtain the numerator, for each `player_id`, check whether the player logged back in
on the day just after the first day they logged in, and COUNT the number of `player_id`s
that meet this requirement.

The denominator is simply the number of DISTINCT `player_id`s.

Result = numerator / denominator


[For Second Solution]
The 3rd SELECT statement gets all the first login dates.
The 2nd SELECT statement gets players who login the day after their first login dates.
The 1st SELECT statement calculates the fraction.
```
<br>

```SQL
CREATE TABLE Activity (player_id int, device_id int, event_date date, games_played int, PRIMARY KEY (player_id, event_date));

INSERT INTO Activity VALUES
(1, 2, '2016-03-01', 5), (1, 2, '2016-03-02', 6),
(2, 3, '2017-06-25', 1), (3, 1, '2016-03-02', 0),
(3, 4, '2018-07-03', 5);


SELECT ROUND(
    IFNULL(
        (
            SELECT COUNT(DISTINCT A.player_id)
            FROM Activity A JOIN Activity B
            ON A.player_id = B.player_id AND DATEDIFF(B.event_date, A.event_date) = 1
            WHERE A.event_date = (
                SELECT MIN(event_date)
                FROM Activity
                WHERE player_id = A.player_id
            )
        ) / (
            SELECT COUNT(DISTINCT player_id)
            FROM Activity
        ), 0
    ), 2
) AS fraction;


SELECT ROUND(A_frac.playerCount / COUNT(DISTINCT A_full.player_id), 2) AS fraction
FROM Activity A_full, (
    SELECT COUNT(DISTINCT A1.player_id) AS playerCount
    FROM Activity A1 INNER JOIN (
        SELECT player_id, MIN(event_date) AS first_login
        FROM Activity
        GROUP BY player_id
    ) A2
    ON A1.player_id = A2.player_id AND DATEDIFF(A1.event_date, A2.first_login) = 1
) A_frac
GROUP BY A_frac.playerCount;
```
***