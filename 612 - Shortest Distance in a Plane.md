```
Table point_2d holds the coordinates (x,y) of some unique points (more than two) in a plane.


Write a query to find the shortest distance between these points rounded to 2 decimals.

| x  | y  |
|----|----|
| -1 | -1 |
| 0  | 0  |
| -1 | -2 |


The shortest distance is 1.00 from point (-1,-1) to (-1,2).

So the output should be:

| shortest |
|----------|
| 1.00     |


Note: The longest distance among all the points are less than 10000.


1. CROSS JOIN finds the distance between the current point and each point
2. Exclude self-connected records (distance = 0)
3. Take MIN, take 2 decimal places
```
<br>

> CROSS JOIN vs. INNER JOIN in SQL [here](https://stackoverflow.com/questions/17759687/cross-join-vs-inner-join-in-sql)
<br>

```SQL
CREATE TABLE point_2d (x int, y int);

INSERT INTO point_2d VALUES (-1, -1), (0, 0), (-1, -2);


SELECT ROUND (
    SQRT (
        MIN(POW(A.x - B.x, 2) + POW(A.y - B.y, 2))
    ), 2
) AS shortest
FROM point_2d A CROSS JOIN point_2d B
WHERE NOT (A.x = B.x AND A.y = B.y)

-- WHERE (A.x != B.x AND A.y != B.y) won't work as it will remove all pairs where x is the same and y is the same


SELECT ROUND(
    SQRT(
        MIN(POW(P1.x - P2.x, 2) + POW(P1.y - P2.y, 2))
    ), 2
) AS shortest
FROM point_2d P1 JOIN point_2d P2
ON P1.x != P2.x OR P1.y != P2.y;
```
***