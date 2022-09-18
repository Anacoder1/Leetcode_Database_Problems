```
Table: Employee

+-------------+------+
| Column Name | Type |
+-------------+------+
| id          | int  |
| salary      | int  |
+-------------+------+

id is the primary key column for this table.

Each row of this table contains information about the salary of an employee.


Write a SQL query to report the second highest salary from the Employee table.
If there is no second highest salary, the query should report null.


The query result format is in the following example.

Example 1:

Input: 
Employee table:
+----+--------+
| id | salary |
+----+--------+
| 1  | 100    |
| 2  | 200    |
| 3  | 300    |
+----+--------+

Output: 
+---------------------+
| SecondHighestSalary |
+---------------------+
| 200                 |
+---------------------+


Example 2:

Input: 
Employee table:
+----+--------+
| id | salary |
+----+--------+
| 1  | 100    |
+----+--------+

Output: 
+---------------------+
| SecondHighestSalary |
+---------------------+
| null                |
+---------------------+
```
<br>

> The third solution is taken from [here](https://leetcode.com/problems/second-highest-salary/discuss/52957/Simple-query-which-handles-the-NULL-situation/54042)
<br>

> The fourth and fifth solutions are taken from [here](https://leetcode.com/problems/second-highest-salary/discuss/1168444/Summary-Five-ways-to-solve-the-top-n-nth-problems)
<br>

```SQL
CREATE TABLE Employee (id int, salary int, PRIMARY KEY (id));

INSERT INTO Employee VALUES
(1, 100), (2, 200), (3, 300);


SELECT (
    SELECT DISTINCT salary
    FROM Employee
    ORDER BY salary DESC
    LIMIT 1 OFFSET 1
) AS SecondHighestSalary;


SELECT IFNULL(
    (
        SELECT DISTINCT salary
        FROM Employee
        ORDER BY salary DESC
        LIMIT 1 OFFSET 1
    ), NULL
) AS SecondHighestSalary;


SELECT MAX(salary) AS SecondHighestSalary
FROM Employee
WHERE salary < (
    SELECT MAX(salary)
    FROM Employee
);


SELECT DISTINCT MAX(salary) AS SecondHighestSalary
FROM Employee A
WHERE salary < (
    SELECT MAX(salary)
    FROM Employee B
    WHERE B.salary > A.salary
);


WITH CTE AS (
    SELECT salary, RANK() OVER (ORDER BY salary DESC) AS rank_desc
    FROM Employee
)
SELECT MAX(salary) AS SecondHighestSalary
FROM CTE
WHERE rank_desc = 2;

-- In above solution, use DENSE_RANK() if more than 1 employees have maximum salary
```
***