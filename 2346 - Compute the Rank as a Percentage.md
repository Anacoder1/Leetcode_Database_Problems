```
Input:

Students table:
+------------+---------------+------+
| student_id | department_id | mark |
+------------+---------------+------+
| 2          | 2             | 650  |
| 8          | 2             | 650  |
| 7          | 1             | 920  |
| 1          | 1             | 610  |
| 3          | 1             | 610  |
| 9          | 1             | 480  |
+------------+---------------+------+

Output: 
+------------+---------------+------------+
| student_id | department_id | percentage |
+------------+---------------+------------+
| 7          | 1             | 0.0        |
| 1          | 1             | 33.3       |
| 3          | 1             | 33.3       |
| 9          | 1             | 100.0      |
| 2          | 2             | 0.0        |
| 8          | 2             | 0.0        |
+------------+---------------+------------+

Explanation: 

For Department 1:
 - Student 7: percentage = (1 - 1) * 100 / (4 - 1) = 0.0
 - Student 1: percentage = (2 - 1) * 100 / (4 - 1) = 33.3
 - Student 3: percentage = (2 - 1) * 100 / (4 - 1) = 33.3
 - Student 9: percentage = (4 - 1) * 100 / (4 - 1) = 100.0

For Department 2:
 - Student 2: percentage = (1 - 1) * 100 / (2 - 1) = 0.0
 - Student 8: percentage = (1 - 1) * 100 / (2 - 1) = 0.0
```
<br>

> The first solution is taken from [here](https://github.com/LeetCode-Feedback/LeetCode-Feedback/issues/8288)
<br>

```SQL
CREATE TABLE Students (student_id int, department_id int, mark int, PRIMARY KEY (student_id, department_id));

INSERT INTO Students VALUES
(2, 2, 650), (8, 2, 650), (7, 1, 920),
(1, 1, 610), (3, 1, 610), (9, 1, 480);


SELECT student_id, department_id,
       IFNULL(
           ROUND(
               ((RANK() OVER (PARTITION BY department_id ORDER BY mark DESC) - 1) * 100)
               /
               (COUNT(student_id) OVER (PARTITION BY department_id) - 1), 2
           ), 0
       ) AS percentage
FROM Students;
```
***