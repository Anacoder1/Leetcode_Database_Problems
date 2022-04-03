```
Table Variables:

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| name          | varchar |
| value         | int     |
+---------------+---------+

name is the primary key for this table.

This table contains the stored variables and their values.


Table Expressions:

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| left_operand  | varchar |
| operator      | enum    |
| right_operand | varchar |
+---------------+---------+

(left_operand, operator, right_operand) is the primary key for this table.

This table contains a boolean expression that should be evaluated.

operator is an enum that takes one of the values ('<', '>', '=')

The values of left_operand and right_operand are guaranteed to be in the Variables table.


Write an SQL query to evaluate the boolean expressions in Expressions table.

Return the result table in any order.

The query result format is in the following example.

Variables table:
+------+-------+
| name | value |
+------+-------+
| x    | 66    |
| y    | 77    |
+------+-------+

Expressions table:
+--------------+----------+---------------+
| left_operand | operator | right_operand |
+--------------+----------+---------------+
| x            | >        | y             |
| x            | <        | y             |
| x            | =        | y             |
| y            | >        | x             |
| y            | <        | x             |
| x            | =        | x             |
+--------------+----------+---------------+

Result table:
+--------------+----------+---------------+-------+
| left_operand | operator | right_operand | value |
+--------------+----------+---------------+-------+
| x            | >        | y             | false |
| x            | <        | y             | true  |
| x            | =        | y             | false |
| y            | >        | x             | true  |
| y            | <        | x             | false |
| x            | =        | x             | true  |
+--------------+----------+---------------+-------+

As shown, you need to find the value of each boolean exprssion in the table using the variables table.


Join tables Expressions and Variables to obtain the values of each variable.

Use IF statement to determine the results.
```
<br>

```SQL
CREATE TABLE Variables (name varchar(32), value int);
CREATE TABLE Expressions (left_operand varchar(32), operator enum('<', '>', '='), right_operand varchar(32));

INSERT INTO Variables VALUES ('x', 66), ('y', 77);

INSERT INTO Expressions VALUES
('x', '>', 'y'), ('x', '<', 'y'), ('x', '=', 'y'),
('y', '>', 'x'), ('y', '<', 'x'), ('x', '=', 'x');


SELECT E.left_operand, E.operator, E.right_operand,
CASE E.operator
    WHEN '>' THEN IF(V1.value > V2.value, 'true', 'false')
    WHEN '<' THEN IF(V1.value < V2.value, 'true', 'false')
    ELSE IF(V1.value = V2.value, 'true', 'false')
END
AS value
FROM Expressions E
LEFT JOIN Variables V1 ON V1.name = E.left_operand
LEFT JOIN Variables V2 ON V2.name = E.right_operand;
```
***