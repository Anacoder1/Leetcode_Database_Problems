```
(couldn't find the complete answer anywhere)
```
<br>

> Solution taken from [this link](https://github.com/kamyu104/LeetCode-Solutions/blob/master/MySQL/the-airport-with-the-most-traffic.sql)

```SQL
WITH flight_counts_cte AS (
    SELECT A.airport_id, SUM(a.flights_count) AS flights_count
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
```
***