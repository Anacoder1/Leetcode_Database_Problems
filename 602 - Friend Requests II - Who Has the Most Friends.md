```
In social networks like Facebook or Twitter, people send friend requests and accept others’ requests as well.

Table : request_accepted

+--------------+-------------+------------+
| requester_id | accepter_id | accept_date|
|--------------|-------------|------------|
| 1            | 2           | 2016_06-03 |
| 1            | 3           | 2016-06-08 |
| 2            | 3           | 2016-06-08 |
| 3            | 4           | 2016-06-09 |
+--------------+-------------+------------+

This table holds the data of friend acceptance, while requester_id and accepter_id both are the id of a person.


Write a query to find the the people who has most friends and the most friends number under the following rules:

- It is guaranteed there is only 1 people having the most friends.
- The friend request could only been accepted once, which mean there is no multiple records with the same requester_id and accepter_id value.

For the sample data above, the result is:

Result table:
+------+------+
| id   | num  |
|------|------|
| 3    | 3    |
+------+------+

The person with id '3' is a friend of people '1', '2' and '4', so he has 3 friends in total, which is the most number than any others.


Follow-up:

In the real world, multiple people could have the same most number of friends, can you find all these people in this case?


The person that has the most friends has the maximum number of occurrences in requester_id and accepter_id, so use UNION ALL to obtain all the values in requester_id and accepter_id, including duplicates.

Then SELECT the corresponding id and the count of the occurrences of id using GROUP BY.

Use ORDER BY and LIMIT to retain the entry with the maximum number of friends.
```
<br>

```SQL
CREATE TABLE request_accepted (requester_id int, accepter_id int, accept_date date);

INSERT INTO request_accepted VALUES
(1, 2, '2016-06-03'), (1, 3, '2016-06-08'),
(2, 3, '2016-06-08'), (3, 4, '2016-06-09');


SELECT id, COUNT(*) AS num
FROM (
    SELECT requester_id AS id FROM request_accepted
    UNION ALL
    SELECT accepter_id FROM request_accepted
) AS friends_count
GROUP BY id
ORDER BY num DESC
LIMIT 1;


SELECT ids AS id, cnt AS num
FROM (
    SELECT ids, COUNT(*) AS cnt
    FROM (
        SELECT requester_id AS ids FROM request_accepted
        UNION ALL
        SELECT accepter_id FROM request_accepted
    ) AS tbl1
GROUP BY ids) AS tbl2
ORDER BY cnt DESC
LIMIT 1;
```
***