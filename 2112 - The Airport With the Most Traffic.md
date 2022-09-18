```
Table: Flights

+-------------------+------+
| Column Name       | Type |
+-------------------+------+
| departure_airport | int  |
| arrival_airport   | int  |
| flights_count     | int  |
+-------------------+------+

(departure_airport, arrival_airport) is the primary key column for this table.

Each row of this table indicates that there were flights_count flights that departed from
departure_airport and arrived at arrival_airport.


Write a SQL query to report the ID of the airport with the most traffic.

The airport with the most traffic is the airport that has the largest total number of flights that either departed from
or arrived at the airport.

If there is more than one airport with the most traffic, report them all.

Return the result table in any order.

The query result format is in the following example.


Example 1:

Input: 
Flights table:
+-------------------+-----------------+---------------+
| departure_airport | arrival_airport | flights_count |
+-------------------+-----------------+---------------+
| 1                 | 2               | 4             |
| 2                 | 1               | 5             |
| 2                 | 4               | 5             |
+-------------------+-----------------+---------------+

Output: 
+------------+
| airport_id |
+------------+
| 2          |
+------------+

Explanation: 
Airport 1 was engaged with 9 flights (4 departures, 5 arrivals).
Airport 2 was engaged with 14 flights (10 departures, 4 arrivals).
Airport 4 was engaged with 5 flights (5 arrivals).
The airport with the most traffic is airport 2.


Example 2:

Input: 
Flights table:
+-------------------+-----------------+---------------+
| departure_airport | arrival_airport | flights_count |
+-------------------+-----------------+---------------+
| 1                 | 2               | 4             |
| 2                 | 1               | 5             |
| 3                 | 4               | 5             |
| 4                 | 3               | 4             |
| 5                 | 6               | 7             |
+-------------------+-----------------+---------------+

Output: 
+------------+
| airport_id |
+------------+
| 1          |
| 2          |
| 3          |
| 4          |
+------------+

Explanation: 
Airport 1 was engaged with 9 flights (4 departures, 5 arrivals).
Airport 2 was engaged with 9 flights (5 departures, 4 arrivals).
Airport 3 was engaged with 9 flights (5 departures, 4 arrivals).
Airport 4 was engaged with 9 flights (4 departures, 5 arrivals).
Airport 5 was engaged with 7 flights (7 departures).
Airport 6 was engaged with 7 flights (7 arrivals).
The airports with the most traffic are airports 1, 2, 3, and 4.
```
<br>

> Solutions Links
<br>
> 1. [here](https://github.com/kamyu104/LeetCode-Solutions/blob/master/MySQL/the-airport-with-the-most-traffic.sql)
> 2. [here](https://www.youtube.com/watch?v=u5hEeI6Q5ww)
> 3. [here](https://blog.51cto.com/u_10980859/5302632)
<br>

```SQL
CREATE TABLE Flights (departure_airport int, arrival_airport int, flights_count int, PRIMARY KEY (departure_airport, arrival_airport));

-- INSERT INTO Flights VALUES
-- (1, 2, 4), (2, 1, 5), (2, 4, 5);

INSERT INTO Flights VALUES
(1, 2, 4), (2, 1, 5), (3, 4, 5), (4, 3, 4), (5, 6, 7);


WITH flight_counts_cte AS (
    SELECT A.airport_id, SUM(A.flights_count) AS flights_count
    FROM (
        SELECT departure_airport AS airport_id, flights_count AS flights_count
        FROM Flights
        UNION ALL
        SELECT arrival_airport AS airport_id, flights_count AS flights_count
        FROM Flights
    ) A
    GROUP BY A.airport_id
    ORDER BY NULL
)
SELECT A.airport_id
FROM flight_counts_cte A, (
    SELECT MAX(B.flights_count) AS max_flights_count
    FROM flight_counts_cte B
) C
WHERE A.flights_count = C.max_flights_count;


WITH CTE AS (
    SELECT departure_airport AS airport_id,
           SUM(flights_count) AS number
    FROM Flights
    GROUP BY departure_airport
), CTE_2 AS (
    SELECT arrival_airport AS airport_id,
           SUM(flights_count) AS number
    FROM Flights
    GROUP BY arrival_airport
), CTE_3 AS (
    SELECT * FROM CTE
    UNION
    SELECT * FROM CTE_2
), CTE_4 AS (
    SELECT airport_id, SUM(number) AS traffic
    FROM CTE_3
    GROUP BY airport_id
)
SELECT airport_id
FROM CTE_4
WHERE traffic = (SELECT MAX(traffic) FROM CTE_4);


WITH CTE_1 AS (
    SELECT departure_airport AS id, flights_count AS cnt
    FROM Flights
    UNION ALL
    SELECT arrival_airport, flights_count
    FROM Flights
), CTE_2 AS (
    SELECT id, RANK() OVER(ORDER BY SUM(cnt) DESC) rnk
    FROM CTE_1
    GROUP BY 1
)
SELECT id AS airport_id
FROM CTE_2
WHERE rnk = 1;
```
***