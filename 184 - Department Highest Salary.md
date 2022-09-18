```
Table: Employee

+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| id           | int     |
| name         | varchar |
| salary       | int     |
| departmentId | int     |
+--------------+---------+

id is the primary key column for this table.

departmentId is a foreign key of the ID from the Department table.

Each row of this table indicates the ID, name, and salary of an employee. It also contains the ID of their department.

 
Table: Department

+-------------+---------+
| Column Name | Type    |
+-------------+---------+
| id          | int     |
| name        | varchar |
+-------------+---------+

id is the primary key column for this table.

Each row of this table indicates the ID of a department and its name.

 
Write an SQL query to find employees who have the highest salary in each of the departments.

Return the result table in any order.

The query result format is in the following example.


Example 1:

Input: 
Employee table:
+----+-------+--------+--------------+
| id | name  | salary | departmentId |
+----+-------+--------+--------------+
| 1  | Joe   | 70000  | 1            |
| 2  | Jim   | 90000  | 1            |
| 3  | Henry | 80000  | 2            |
| 4  | Sam   | 60000  | 2            |
| 5  | Max   | 90000  | 1            |
+----+-------+--------+--------------+

Department table:
+----+-------+
| id | name  |
+----+-------+
| 1  | IT    |
| 2  | Sales |
+----+-------+

Output: 
+------------+----------+--------+
| Department | Employee | Salary |
+------------+----------+--------+
| IT         | Jim      | 90000  |
| Sales      | Henry    | 80000  |
| IT         | Max      | 90000  |
+------------+----------+--------+

Explanation: Max and Jim both have the highest salary in the IT department and Henry has the highest salary in the Sales department.


```
<br>

> Second and Third solutions taken from [here](https://leetcode.com/problems/department-highest-salary/discuss/53607/Three-accpeted-solutions)
<br>

> Fourth solution taken from [here](https://leetcode.com/problems/department-highest-salary/discuss/53608/Simple-solution-easy-to-understand)
<br>

```SQL
CREATE TABLE Department (id int, name varchar(32), PRIMARY KEY (id));

CREATE TABLE Employee (id int, name varchar(32), salary int, departmentId int, PRIMARY KEY (id),
    FOREIGN KEY (departmentId) REFERENCES Department (id));

INSERT INTO Department VALUES
(1, 'IT'), (2, 'Sales');

INSERT INTO Employee VALUES
(1, 'Joe', 70000, 1), (2, 'Jim', 90000, 1),
(3, 'Henry', 80000, 2), (4, 'Sam', 60000, 2),
(5, 'Max', 90000, 1);


SELECT D.name AS 'Department', E.name AS 'Employee', E.salary AS 'Salary'
FROM Employee E JOIN Department D
ON E.departmentId = D.id
WHERE (E.departmentId, E.salary) IN (
    SELECT departmentId, MAX(salary)
    FROM Employee
    GROUP BY departmentId
);


SELECT D.name AS 'Department', E.name AS 'Employee', E.salary AS 'Salary'
FROM Employee E, (
    SELECT departmentId, MAX(Salary) AS max
    FROM Employee
    GROUP BY departmentId
) T, Department D
WHERE E.departmentId = T.departmentId
AND E.salary = T.max
AND E.departmentId = D.id;


SELECT D.name AS 'Department', A.name AS 'Employee', A.salary AS 'Salary'
FROM Employee A, Department D
WHERE A.departmentId = D.id
AND NOT EXISTS (
    SELECT 1
    FROM Employee B
    WHERE B.salary > A.salary AND A.departmentId = B.departmentId
);


SELECT D.name AS 'Department', E.name AS 'Employee', E.salary AS 'Salary'
FROM Department D, Employee E
WHERE E.departmentId = D.id
AND E.salary = (
    SELECT MAX(salary)
    FROM Employee E2
    WHERE E2.departmentId = D.id
);
```
***