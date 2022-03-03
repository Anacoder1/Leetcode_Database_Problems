```
Table: Employees

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| employee_id   | int     |
| employee_name | varchar |
| manager_id    | int     |
+---------------+---------+

employee_id is the primary key for this table.

Each row of this table indicates that the employee with ID employee_id and name employee_name reports his work to his/her direct manager with manager_id

The head of the company is the employee with employee_id = 1.


Write an SQL query to find employee_id of all employees that directly or indirectly report their work to the head of the company.

The indirect relation between managers will not exceed 3 managers as the company is small.

Return result table in any order without duplicates.

The query result format is in the following example:

Employees table:
+-------------+---------------+------------+
| employee_id | employee_name | manager_id |
+-------------+---------------+------------+
| 1           | Boss          | 1          |
| 3           | Alice         | 3          |
| 2           | Bob           | 1          |
| 4           | Daniel        | 2          |
| 7           | Luis          | 4          |
| 8           | Jhon          | 3          |
| 9           | Angela        | 8          |
| 77          | Robert        | 1          |
+-------------+---------------+------------+

Result table:
+-------------+
| employee_id |
+-------------+
| 2           |
| 77          |
| 4           |
| 7           |
+-------------+

The head of the company is the employee with employee_id 1.
The employees with employee_id 2 and 77 report their work directly to the head of the company.
The employee with employee_id 4 report his work indirectly to the head of the company 4 --> 2 --> 1. 
The employee with employee_id 7 report his work indirectly to the head of the company 7 --> 4 --> 2 --> 1.
The employees with employee_id 3, 8 and 9 don't report their work to head of company directly or indirectly.


Since the indirect relation between managers will not exceed 3 managers, consider the three cases of different number of managers of the direct/indirect relation between managers.

1. An employee directly reports to the head of the company.
2. An employee directly reports to a manager, and the manager directly reports to the head of the company.
3. An employee directly reports to manager 1, manager 1 directly reports to manager 2, and manager 2 directly reports to the head of the company.

For each case, select the corresponding employees and union the results.

The first search : through the leader id=1, find the employees I id=2 and id=77 who directly report to the boss
                           UNION
Second search : Use the first query result to find the employee II id=4 that reports to employee I,
                           UNION
The third search : Use the second search result to find the employee III id=7 that reports to the employee II.

Finally, it will be arranged according to the size of the employee number.
```
<br>

```SQL
SELECT A.employee_id AS employee_id
FROM 
Employees AS A 
LEFT JOIN Employees AS B ON A.manager_id = B.employee_id
LEFT JOIN Employees AS C ON B.manager_id = C.employee_id
LEFT JOIN Employees AS D ON C.manager_id = D.employee_id
WHERE A.employee_id != 1 AND D.employee_id = 1;


SELECT employee_id FROM Employees
WHERE manager_id IN (
    SELECT employee_id FROM Employees WHERE manager_id IN (
        SELECT employee_id FROM Employees WHERE manager_id = 1
    )
)
AND employee_id != 1;


SELECT A.employee_id FROM Employees A
JOIN Employees B JOIN Employees C
WHERE A.manager_id = B.employee_id AND B.manager_id = C.employee_id AND C.manager_id = 1 AND A.employee_id != C.manager_id;
```
***