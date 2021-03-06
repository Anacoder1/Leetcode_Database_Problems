```
Table: Employee

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| employee_id   | int     |
| team_id       | int     |
+---------------+---------+

employee_id is the primary key for this table.

Each row of this table contains the ID of each employee and their respective team.


Write an SQL query to find the team size of each of the employees.

Return result table in any order.

The query result format is in the following example:

Employee Table:
+-------------+------------+
| employee_id | team_id    |
+-------------+------------+
|     1       |     8      |
|     2       |     8      |
|     3       |     8      |
|     4       |     7      |
|     5       |     9      |
|     6       |     9      |
+-------------+------------+

Result table:
+-------------+------------+
| employee_id | team_size  |
+-------------+------------+
|     1       |     3      |
|     2       |     3      |
|     3       |     3      |
|     4       |     1      |
|     5       |     2      |
|     6       |     2      |
+-------------+------------+

Employees with Id 1,2,3 are part of a team with team_id = 8.

Employees with Id 4 is part of a team with team_id = 7.

Employees with Id 5,6 are part of a team with team_id = 9.


Join table Employee with itself using the team_id field.

Then for each employee_id, obtain the no. of entries and use it as team_size.
```
<br>

```SQL
SELECT E1.employee_id, COUNT(*) AS team_size
FROM Employee E1 LEFT JOIN Employee E2
ON E1.team_id = E2.team_id
GROUP BY E1.employee_id;


SELECT E1.employee_id, COUNT(E2.employee_id) AS team_size
FROM Employee E1 INNER JOIN Employee E2
ON E1.team_id = E2.team_id
GROUP BY E1.employee_id, E2.team_id;
```
***