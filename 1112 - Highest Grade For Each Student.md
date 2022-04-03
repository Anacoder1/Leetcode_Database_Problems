```
Table: Enrollments

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| student_id    | int     |
| course_id     | int     |
| grade         | int     |
+---------------+---------+

(student_id, course_id) is the primary key of this table.


Write a SQL query to find the highest grade with its corresponding course for each student.

In case of a tie, you should find the course with the smallest course_id.

The output must be sorted by increasing student_id.

The query result format is in the following example:

Enrollments table:
+------------+-------------------+
| student_id | course_id | grade |
+------------+-----------+-------+
| 2          | 2         | 95    |
| 2          | 3         | 95    |
| 1          | 1         | 90    |
| 1          | 2         | 99    |
| 3          | 1         | 80    |
| 3          | 2         | 75    |
| 3          | 3         | 82    |
+------------+-----------+-------+

Result table:
+------------+-------------------+
| student_id | course_id | grade |
+------------+-----------+-------+
| 1          | 2         | 99    |
| 2          | 2         | 95    |
| 3          | 3         | 82    |
+------------+-----------+-------+


For each student, find the highest grade.

Then select student, MIN(course_id) as course_id and grade from table Enrollments using the students' ids and the highest grades selected before.

Finally, the result should be grouped by student_id (and grade) so that each student’s highest grade is in the result.
```
<br>

```SQL
CREATE TABLE Enrollments (student_id int , course_id int, grade int, PRIMARY KEY (student_id, course_id));

INSERT INTO Enrollments VALUES
(2, 2, 95), (2, 3, 95), (1, 1, 90),
(1, 2, 99), (3, 1, 80), (3, 2, 75),
(3, 3, 82);


SELECT student_id, MIN(course_id) AS course_id, grade
FROM Enrollments
WHERE (student_id, grade) IN (
    SELECT student_id, MAX(grade)
    FROM Enrollments
    GROUP BY student_id
)
GROUP BY student_id, grade;


SELECT E1.student_id, MIN(E1.course_id) AS course_id, E1.grade
FROM Enrollments E1
WHERE E1.grade = (
    SELECT MAX(grade) AS max_grade
    FROM Enrollments E2
    WHERE E1.student_id = E2.student_id
)
GROUP BY E1.student_id, E1.grade
ORDER BY E1.student_id;
```
***