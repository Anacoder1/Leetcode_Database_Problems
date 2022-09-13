```
The Employee table holds all employees including their managers.

Every employee has an Id, and there is also a column for the manager Id.

+------+----------+-----------+----------+
|Id    |Name 	  |Department |ManagerId |
+------+----------+-----------+----------+
|101   |John 	  |A 	      |null      |
|102   |Dan 	  |A 	      |101       |
|103   |James 	  |A 	      |101       |
|104   |Amy 	  |A 	      |101       |
|105   |Anne 	  |A 	      |101       |
|106   |Ron 	  |B 	      |101       |
+------+----------+-----------+----------+


Given the Employee table, write a SQL query that finds out managers with at least 5 direct reports.

For the above table, your SQL query should return:

+-------+
| Name  |
+-------+
| John  |
+-------+


Note:
No one would report to himself.
```
<br>

```SQL
CREATE TABLE Employee (Id int, Name varchar(32), Department varchar(32), ManagerId int);

INSERT INTO Employee VALUES
(101, 'John', 'A', null), (102, 'Dan', 'A', 101),
(103, 'James', 'A', 101), (104, 'Amy', 'A', 101),
(105, 'Anne', 'A', 101), (106, 'Ron', 'B', 101);


SELECT Name
FROM Employee AS A JOIN (
    SELECT ManagerId
    FROM Employee
    GROUP BY ManagerId
    HAVING COUNT(ManagerId) >= 5
) AS B
ON A.Id = B.ManagerId;
```
***