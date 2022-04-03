```
Table: Project

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| project_id  | int     |
| employee_id | int     |
+-------------+---------+

(project_id, employee_id) is the primary key of this table.

employee_id is a foreign key to Employee table.


Table: Employee

+------------------+---------+
| Column Name      | Type    |
+------------------+---------+
| employee_id      | int     |
| name             | varchar |
| experience_years | int     |
+------------------+---------+

employee_id is the primary key of this table.


Write an SQL query that reports the most experienced employees in each project. In case of a tie, report all employees with the maximum number of experience years.

The query result format is in the following example:

Project table:
+-------------+-------------+
| project_id  | employee_id |
+-------------+-------------+
| 1           | 1           |
| 1           | 2           |
| 1           | 3           |
| 2           | 1           |
| 2           | 4           |
+-------------+-------------+

Employee table:
+-------------+--------+------------------+
| employee_id | name   | experience_years |
+-------------+--------+------------------+
| 1           | Khaled | 3                |
| 2           | Ali    | 2                |
| 3           | John   | 3                |
| 4           | Doe    | 2                |
+-------------+--------+------------------+

Result table:
+-------------+---------------+
| project_id  | employee_id   |
+-------------+---------------+
| 1           | 1             |
| 1           | 3             |
| 2           | 1             |
+-------------+---------------+

Both employees with id 1 and 3 have the most experience among the employees of the first project.

For the second project, the employee with id 1 has the most experience.


Join the tables Project and Employee using the field employee_id.

Select the fields project_id and employee_id using MAX(experience_years).
```
<br>

```SQL
CREATE TABLE Project (project_id int, employee_id int);
CREATE TABLE Employee (employee_id int, name varchar(32), experience_years int);

INSERT INTO Project VALUES 
(1, 1), (1, 2), (1, 3), (2, 1), (2, 4);

INSERT INTO Employee VALUES
(1, 'Khaled', 3), (2, 'Ali', 2),
(3, 'John', 3), (4, 'Doe', 2);


SELECT P.project_id, P.employee_id
FROM Project P LEFT JOIN Employee E
ON P.employee_id = E.employee_id
WHERE (P.project_id, E.experience_years) IN (
    SELECT P.project_id, MAX(E.experience_years)
    FROM Project P LEFT JOIN Employee E
    ON P.employee_id = E.employee_id
    GROUP BY project_id
);


SELECT project_id, employee_id
FROM Project JOIN Employee
USING (employee_id)
WHERE (project_id, experience_years) IN (
    SELECT project_id, MAX(experience_years)
    FROM Project JOIN Employee
    USING (employee_id)
    GROUP BY project_id
);
```
***