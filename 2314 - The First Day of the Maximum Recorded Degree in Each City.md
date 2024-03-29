```
Table: Weather

+-------------+------+
| Column Name | Type |
+-------------+------+
| city_id     | int  |
| day         | date |
| degree      | int  |
+-------------+------+

(city_id, day) is the primary key for this table.

Each row in this table contains the degree of the weather of a city on a certain day.

All the degrees are recorded in the year 2022.


Write a SQL query to report the day that has the maximum recorded degree in each city.

If the maximum degree was recorded for the same city multiple times, return the earliest day among them.

Return the result table ordered by city_id in ascending order.

The query result format is shown in the following example.

Example 1:

Input: 
Weather table:
+---------+------------+--------+
| city_id | day        | degree |
+---------+------------+--------+
| 1       | 2022-01-07 | -12    |
| 1       | 2022-03-07 | 5      |
| 1       | 2022-07-07 | 24     |
| 2       | 2022-08-07 | 37     |
| 2       | 2022-08-17 | 37     |
| 3       | 2022-02-07 | -7     |
| 3       | 2022-12-07 | -6     |
+---------+------------+--------+

Output: 
+---------+------------+--------+
| city_id | day        | degree |
+---------+------------+--------+
| 1       | 2022-07-07 | 24     |
| 2       | 2022-08-07 | 37     |
| 3       | 2022-12-07 | -6     |
+---------+------------+--------+

Explanation: 
For city 1, the maximum degree was recorded on 2022-07-07 with 24 degrees.

For city 2, the maximum degree was recorded on 2022-08-07 and 2022-08-17 with 37 degrees.
We choose the earlier date (2022-08-07).

For city 3, the maximum degree was recorded on 2022-12-07 with -6 degrees.
```
<br>

> Solutions Links
<br>
> 1. [here](https://github.com/kamyu104/LeetCode-Solutions/blob/master/MySQL/the-first-day-of-the-maximum-recorded-degree-in-each-city.sql)
> 2. [here](https://velog.io/@dev_taehyun/algorithm-leetcode-2314)
> 3. [here](https://www.youtube.com/watch?v=5WpBM119_OE)
<br>

```SQL
CREATE TABLE Weather (city_id int, day date, degree int, PRIMARY KEY (city_id, day));

INSERT INTO Weather VALUES
(1, '2022-01-07', -12), (1, '2022-03-07', 5),
(1, '2022-07-07', 24), (2, '2022-08-07', 37),
(2, '2022-08-17', 37), (3, '2022-02-07', -7),
(3, '2022-12-07', -6);


WITH city_day_degree_rank_cte AS (
    SELECT city_id, day, degree,
          ROW_NUMBER() OVER(PARTITION BY city_id ORDER BY degree DESC, day ASC) rnk
    FROM Weather
)
SELECT city_id, day, degree
FROM city_day_degree_rank_cte
WHERE rnk = 1
ORDER BY 1;


SELECT city_id, day, degree
FROM (
    SELECT city_id, day, degree,
           RANK() OVER(PARTITION BY city_id ORDER BY degree DESC, day ASC) AS weather_rank
    FROM Weather
) AS WeatherRanks
WHERE weather_rank = 1
ORDER BY city_id ASC;


WITH cte AS (
    SELECT *, DENSE_RANK() OVER(PARTITION BY city_id ORDER BY degree DESC, day ASC) AS rnk
    FROM Weather
)
SELECT city_id, day, degree
FROM cte
WHERE rnk = 1
ORDER BY city_id ASC;
```
***