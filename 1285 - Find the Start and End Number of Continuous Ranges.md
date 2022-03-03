```
Table: Logs

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| log_id        | int     |
+---------------+---------+

id is the primary key for this table.

Each row of this table contains the ID in a log Table.


Since some IDs have been removed from Logs, Write an SQL query to find the start and end number of continuous ranges in table Logs.

Order the result table by start_id.

The query result format is in the following example:

Logs table:
+------------+
| log_id     |
+------------+
| 1          |
| 2          |
| 3          |
| 7          |
| 8          |
| 10         |
+------------+

Result table:
+------------+--------------+
| start_id   | end_id       |
+------------+--------------+
| 1          | 3            |
| 7          | 8            |
| 10         | 10           |
+------------+--------------+

The result table should contain all ranges in table Logs.
From 1 to 3 is contained in the table.
From 4 to 6 is missing in the table
From 7 to 8 is contained in the table.
Number 9 is missing in the table.
Number 10 is contained in the table.


For each value ‘log’ in the Logs table, if (log-1) doesn’t exist in Logs, then log is the start id of an interval.

If (log+1) doesn’t exist in Logs, then log is the end id of an interval.

Join the start ids and end ids to obtain the result.
```
<br>

```SQL
SELECT log_start.log_id AS start_id, MIN(log_end.log_id) AS end_id
FROM
(SELECT log_id FROM Logs WHERE log_id - 1 NOT IN (SELECT * FROM Logs)) log_start,
(SELECT log_id FROM Logs WHERE log_id + 1 NOT IN (SELECT * FROM Logs)) log_end
WHERE log_start.log_id <= log_end.log_id
GROUP BY log_start.log_id;


SELECT MIN(log_id) AS start_id, MAX(log_id) AS end_id
FROM (
    SELECT L1.log_id, (L1.log_id - L1.row_num) AS diff
    FROM (
        SELECT log_id, ROW_NUMBER() OVER() AS row_num FROM Logs
    ) L1
) L2
GROUP BY diff;
```
***