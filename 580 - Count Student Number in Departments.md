```
A university uses 2 data tables, student and department, to store data about its students and the departments associated with each major.

Write a query to print the respective department name and number of students majoring in each department for all departments in the department table (even ones with no current students).

Sort your results by descending number of students; if two or more departments have the same number of students, then sort those departments alphabetically by department name.

The student is described as follow:

| Column Name  | Type      |
|--------------|-----------|
| student_id   | Integer   |
| student_name | String    |
| gender       | Character |
| dept_id      | Integer   |

where student_id is the student’s ID number, student_name is the student’s name, gender is their gender, and dept_id is the department ID associated with their declared major.

And the department table is described as below:

| Column Name | Type    |
|-------------|---------|
| dept_id     | Integer |
| dept_name   | String  |

where dept_id is the department’s ID number and dept_name is the department name.


Here is an example input:

student table:

| student_id | student_name | gender | dept_id |
|------------|--------------|--------|---------|
| 1          | Jack         | M      | 1       |
| 2          | Jane         | F      | 1       |
| 3          | Mark         | M      | 2       |

department table:

| dept_id | dept_name   |
|---------|-------------|
| 1       | Engineering |
| 2       | Science     |
| 3       | Law         |

The Output should be:

| dept_name   | student_number |
|-------------|----------------|
| Engineering | 2              |
| Science     | 1              |
| Law         | 0              |


JOIN the tables `department` and `student`

Use `department LEFT JOIN student ON department.dept_id = student.dept_id` so that all the departments are included in the output.

Then, SELECT entries using `SELECT dept_name, COUNT(student_id) AS student_number` AND `GROUP BY department.dept_id`.

To order the results, use `ORDER BY student_number DESC, dept_name ASC`.
```
<br>

```SQL
CREATE TABLE Student (student_id int, student_name varchar(32), gender char(1), dept_id int);
CREATE TABLE Department (dept_id int, dept_name varchar(32));

INSERT INTO Student VALUES
(1, 'Jack', 'M', 1), (2, 'Jane', 'F', 1), (3, 'Mark', 'M', 2);

INSERT INTO Department VALUES
(1, 'Engineering'), (2, 'Science'), (3, 'Law');


SELECT dept_name, COUNT(student_id) AS student_number
FROM Department LEFT JOIN Student ON Department.dept_id = Student.dept_id
GROUP BY Department.dept_name
ORDER BY student_number DESC, dept_name ASC;


SELECT D.dept_name, COUNT(student_id) AS student_number
FROM Department D LEFT OUTER JOIN Student S
ON D.dept_id = S.dept_id
GROUP BY D.dept_name
ORDER BY student_number DESC, D.dept_name;
```
***