```
Table: Customer

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| customer_id   | int     |
| name          | varchar |
| visited_on    | date    |
| amount        | int     |
+---------------+---------+

(customer_id, visited_on) is the primary key for this table.

This table contains data about customer transactions in a restaurant.

visited_on is the date on which the customer with ID (customer_id) have visited the restaurant.

amount is the total paid by a customer.


You are the restaurant owner and you want to analyze a possible expansion (there will be at least one customer every day).

Write an SQL query to compute moving average of how much customer paid in a 7 days window (current day + 6 days before) .

Return result table ordered by visited_on.

average_amount should be rounded to 2 decimal places, all dates are in the format (‘YYYY-MM-DD’).

The query result format is in the following example:

Customer table:
+-------------+--------------+--------------+-------------+
| customer_id | name         | visited_on   | amount      |
+-------------+--------------+--------------+-------------+
| 1           | Jhon         | 2019-01-01   | 100         |
| 2           | Daniel       | 2019-01-02   | 110         |
| 3           | Jade         | 2019-01-03   | 120         |
| 4           | Khaled       | 2019-01-04   | 130         |
| 5           | Winston      | 2019-01-05   | 110         | 
| 6           | Elvis        | 2019-01-06   | 140         | 
| 7           | Anna         | 2019-01-07   | 150         |
| 8           | Maria        | 2019-01-08   | 80          |
| 9           | Jaze         | 2019-01-09   | 110         | 
| 1           | Jhon         | 2019-01-10   | 130         | 
| 3           | Jade         | 2019-01-10   | 150         | 
+-------------+--------------+--------------+-------------+

Result table:
+--------------+--------------+----------------+
| visited_on   | amount       | average_amount |
+--------------+--------------+----------------+
| 2019-01-07   | 860          | 122.86         |
| 2019-01-08   | 840          | 120            |
| 2019-01-09   | 840          | 120            |
| 2019-01-10   | 1000         | 142.86         |
+--------------+--------------+----------------+

1st moving average from 2019-01-01 to 2019-01-07 has an average_amount of (100 + 110 + 120 + 130 + 110 + 140 + 150)/7 = 122.86

2nd moving average from 2019-01-02 to 2019-01-08 has an average_amount of (110 + 120 + 130 + 110 + 140 + 150 + 80)/7 = 120

3rd moving average from 2019-01-03 to 2019-01-09 has an average_amount of (120 + 130 + 110 + 140 + 150 + 80 + 110)/7 = 120

4th moving average from 2019-01-04 to 2019-01-10 has an average_amount of (130 + 110 + 140 + 150 + 80 + 110 + 130 + 150)/7 = 142.86


First, only select the values of visited_on at least greater than the minimum value of visited_on by at least 6. 

For example, if the minimum value of visited_on is ‘2019-01-01’, then only select the values of visited_on that are at least ‘2019-01-07’.

Then, for each date in selected visited_on, obtain all the entries in the 7-day window, and calculate the sum and the average.

Use ROUND to round the average to 2 decimal placed.
```
<br>

```SQL
CREATE TABLE Customer (customer_id int, name varchar(32), visited_on date, amount int);

INSERT INTO Customer VALUES
(1, 'Jhon', '2019-01-01', 100), (2, 'Daniel', '2019-01-02', 110),
(3, 'Jade', '2019-01-03', 120), (4, 'Khaled', '2019-01-04', 130),
(5, 'Winston', '2019-01-05', 110), (6, 'Elvis', '2019-01-06', 140),
(7, 'Anna', '2019-01-07', 150), (8, 'Maria', '2019-01-08', 80),
(9, 'Jaze', '2019-01-09', 110), (1, 'Jhon', '2019-01-10', 130),
(3, 'Jade', '2019-01-10', 150);


SELECT Visits.visited_on AS visited_on, SUM(C.amount) AS amount, ROUND(SUM(C.amount) / 7.0, 2) AS average_amount
FROM (
    SELECT DISTINCT visited_on
    FROM Customer
    WHERE DATEDIFF(visited_on, (SELECT MIN(visited_on) FROM Customer)) >= 6
) Visits LEFT JOIN Customer C
ON DATEDIFF(Visits.visited_on, C.visited_on) BETWEEN 0 AND 6
GROUP BY Visits.visited_on
ORDER BY visited_on;


SELECT C1.visited_on, SUM(C2.amount) AS amount, ROUND(AVG(C2.amount), 2) AS average_amount
FROM (
    SELECT visited_on, SUM(amount) AS amount
    FROM Customer
    GROUP BY visited_on
) C1
JOIN (
    SELECT visited_on, SUM(amount) AS amount
    FROM Customer
    GROUP BY visited_on
) C2
ON DATEDIFF(C1.visited_on, C2.visited_on) BETWEEN 0 AND 6
GROUP BY C1.visited_on
HAVING COUNT(C2.amount) = 7;
```
***