```
In Facebook, there is a `Follow` table with two columns: followee, follower.

Please write a SQL query to get the amount of each follower’s follower if he/she has one.

For example:
+-------------+------------+
| followee    | follower   |
+-------------+------------+
|     A       |     B      |
|     B       |     C      |
|     B       |     D      |
|     D       |     E      |
+-------------+------------+

should output:
+-------------+------------+
| follower    | num        |
+-------------+------------+
|     B       |  2         |
|     D       |  1         |
+-------------+------------+

Explanation:
Both B and D exist in the follower list, when as a followee, B’s follower is C and D, and D’s follower is E.
A does not exist in follower list.

NOTE:
- Followee would not follow himself/herself in all cases.
- Please display the result in follower’s alphabet order.


Decompose this problem into 2 steps.

1. SELECT each follower's no. of followers, where the followers with 0 followers
   are also selected.
2. SELECT the entries from the first step that have numbers > 0

For step 1, JOIN `Follow` table with itself and use `COUNT(DISTINCT)` and `GROUP BY`
to obtain the no. of followers of each follower.

For step 2, based on the result of step 1, only SELECT the entries with `num` > 0,
and sort the entries according to `follower` in ascending order.
```
<br>

```SQL
CREATE TABLE Follow (followee varchar(32), follower varchar(32));

INSERT INTO Follow VALUES
('A', 'B'), ('B', 'C'), ('B', 'D'), ('D', 'E');


SELECT follower, num
FROM (
    SELECT F1.follower AS follower, COUNT(DISTINCT F2.follower) AS num
    FROM Follow F1 LEFT JOIN Follow F2
    ON F1.follower = F2.followee
    GROUP BY F1.follower
) AS nums
WHERE num > 0
ORDER BY follower;


SELECT F1.follower, COUNT(DISTINCT F2.follower) AS num
FROM Follow F1 JOIN Follow F2
ON F1.follower = F2.followee
GROUP BY F1.follower;
```
***