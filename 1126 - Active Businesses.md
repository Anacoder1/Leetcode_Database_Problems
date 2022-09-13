```
Table: Events

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| business_id   | int     |
| event_type    | varchar |
| occurences    | int     | 
+---------------+---------+

(business_id, event_type) is the primary key of this table.

Each row in the table logs the info that an event of some type occured at some business for a number of times.


Write an SQL query to find all active businesses.

An active business is a business that has more than one event type with occurences greater than the average occurences of that event type among all businesses.

The query result format is in the following example:

Events table:
+-------------+------------+------------+
| business_id | event_type | occurences |
+-------------+------------+------------+
| 1           | reviews    | 7          |
| 3           | reviews    | 3          |
| 1           | ads        | 11         |
| 2           | ads        | 7          |
| 3           | ads        | 6          |
| 1           | page views | 3          |
| 2           | page views | 12         |
+-------------+------------+------------+

Result table:
+-------------+
| business_id |
+-------------+
| 1           |
+-------------+ 

Average for 'reviews', 'ads' and 'page views' are (7 + 3) / 2 = 5, (11 + 7 + 6) / 3 = 8, (3 + 12) / 2 = 7.5 respectively.

Business with id 1 has 7 'reviews' events (more than 5) and 11 'ads' events (more than 8) so it is an active business.


SELECT each event_type and calculate the average of occurrences from Events table.

Then JOIN the selection result with the original Events table using the field event_type, and keep the business_ids such that more than one event type with occurrences greater than the average occurrences of that event type.
```
<br>

```SQL
CREATE TABLE Events (business_id int, event_type varchar(32), occurences int);

INSERT INTO Events VALUES
(1, 'reviews', 7), (3, 'reviews', 3),
(1, 'ads', 11), (2, 'ads', 7), (3, 'ads', 6),
(1, 'page views', 3), (2, 'page views', 12);


SELECT business_id
FROM Events E1 RIGHT JOIN (
    SELECT event_type, AVG(occurrences) AS avg_occurences
    FROM Events
    GROUP BY event_type
) E2
ON E1.event_type = E2.event_type
GROUP BY E1.business_id
HAVING SUM(IF(E1.occurences > E2.avg_occurences, 1, 0)) > 1;


SELECT business_id
FROM (
    SELECT *, AVG(occurences) OVER (PARTITION BY event_type) AS average
    FROM Events
) T1
WHERE occurences > average
GROUP BY business_id
HAVING COUNT(business_id) > 1;
```
***