```
Table: Points

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| id            | int     |
| x_value       | int     |
| y_value       | int     |
+---------------+---------+

id is the primary key for this table.

Each point is represented as a 2D Dimensional (x_value, y_value).


Write an SQL query to report of all possible rectangles which can be formed by any two points of the table.

Each row in the result contains three columns (p1, p2, area) where:

- p1 and p2 are the id of two opposite corners of a rectangle and p1 < p2.
- Area of this rectangle is represented by the column area.

Report the query in descending order by area in case of tie in ascending order by p1 and p2.

Points table:
+----------+-------------+-------------+
| id       | x_value     | y_value     |
+----------+-------------+-------------+
| 1        | 2           | 8           |
| 2        | 4           | 7           |
| 3        | 2           | 10          |
+----------+-------------+-------------+

Result table:
+----------+-------------+-------------+
| p1       | p2          | area        |
+----------+-------------+-------------+
| 2        | 3           | 6           |
| 1        | 2           | 2           |
+----------+-------------+-------------+

p1 should be less than p2 and area greater than 0.
p1 = 1 and p2 = 2, has an area equal to |2-4| * |8-7| = 2.
p1 = 2 and p2 = 3, has an area equal to |4-2| * |7-10| = 6.
p1 = 1 and p2 = 3 It's not possible because the rectangle has an area equal to 0.


For each pair of points p1 and p2, if their x_values and y_values are both different, then they can form a rectangle, where the area of the rectangle can be calculated using their x_values and y_values.

Select such pairs of points and calculate the areas accordingly.

Sort the result table according to area in descending order and then according to p1 and p2 in ascending order.
```
<br>

```SQL
CREATE TABLE Points(id int, x_value int, y_value int);

INSERT INTO Points VALUES
(1, 2, 8), (2, 4, 7), (3, 2, 10);


SELECT point1.id AS p1, point2.id AS p2, ABS((point1.x_value - point2.x_value) * (point1.y_value - point2.y_value)) AS area
FROM Points point1 JOIN Points point2 ON point1.id < point2.id
WHERE point1.x_value != point2.x_value AND point1.y_value != point2.y_value
ORDER BY area DESC, p1, p2;


SELECT *
FROM (
    SELECT A.id AS p1, B.id AS p2, (
        ABS(A.x_value - B.x_value) * ABS(A.y_value - B.y_value)
    ) AS area
    FROM Points A, Points B
    WHERE A.id != B.id AND A.id < B.id
) T
WHERE area != 0
ORDER BY area DESC, p1, p2;
```
***