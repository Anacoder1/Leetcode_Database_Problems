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

 
Write a SQL query to report the n-th highest salary from the Employee table.

If there is no nth highest salary, the query should report null.

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

n = 2
Output: 
+------------------------+
| getNthHighestSalary(2) |
+------------------------+
| 200                    |
+------------------------+


Example 2:

Input: 
Employee table:
+----+--------+
| id | salary |
+----+--------+
| 1  | 100    |
+----+--------+

n = 2
Output: 
+------------------------+
| getNthHighestSalary(2) |
+------------------------+
| null                   |
+------------------------+
```
<br>

```SQL
CREATE TABLE Employee (id int, salary int, PRIMARY KEY (id));

INSERT INTO Employee VALUES (1, 100), (2, 200), (3, 300);


[here](https://leetcode.com/problems/nth-highest-salary/discuss/53041/Accpted-Solution-for-the-Nth-Highest-Salary)
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
DECLARE M INT;
SET M = N - 1;
RETURN (
    SELECT DISTINCT salary
    FROM Employee
    ORDER BY salary DESC
    LIMIT M, 1    -- same as `LIMIT 1 OFFSET M`
);
END


[here](https://leetcode.com/problems/nth-highest-salary/discuss/53041/Accpted-Solution-for-the-Nth-Highest-Salary/54145)
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
SET N = N - 1;
RETURN (
    SELECT DISTINCT(salary)
    FROM Employee
    UNION
    SELECT NULL
    ORDER BY salary DESC LIMIT 1 OFFSET N
);
END


[here](https://circlecoder.com/nth-highest-salary/)
CREATE FUNCTION getNthHighestSalary(N int) RETURNS int
BEGIN
RETURN (
    SELECT DISTINCT salary AS 'getNthHighestSalary(2)'
    FROM (
        SELECT salary, DENSE_RANK() OVER (ORDER BY salary DESC) AS rnk
        FROM Employee
    ) sal_rnk
    WHERE rnk = N
);
END


[here](https://github.com/grandyang/leetcode/issues/177)
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
RETURN (
    SELECT MAX(salary)
    FROM Employee E1
    WHERE N - 1 = (
        SELECT COUNT(DISTINCT(E2.salary))
        FROM Employee E2
        WHERE E2.salary > E1.salary
    )    
);
END

-- Solution obtained by Changing N-1 to N AND > to >= together
-- in the code above will also work.

-- Invoked using line below [NOT TESTED]
getNthHighestSalary(2);
```
***