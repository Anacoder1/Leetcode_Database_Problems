```
Table: Views

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| article_id    | int     |
| author_id     | int     |
| viewer_id     | int     |
| view_date     | date    |
+---------------+---------+

There is no primary key for this table, it may have duplicate rows.

Each row of this table indicates that some viewer viewed an article (written by some author) on some date. 

Note that equal author_id and viewer_id indicate the same person.


Write a SQL query to find all the people who viewed more than one article on the same date, sorted in ascending order by their id.

The query result format is in the following example:

Views table:
+------------+-----------+-----------+------------+
| article_id | author_id | viewer_id | view_date  |
+------------+-----------+-----------+------------+
| 1          | 3         | 5         | 2019-08-01 |
| 3          | 4         | 5         | 2019-08-01 |
| 1          | 3         | 6         | 2019-08-02 |
| 2          | 7         | 7         | 2019-08-01 |
| 2          | 7         | 6         | 2019-08-02 |
| 4          | 7         | 1         | 2019-07-22 |
| 3          | 4         | 4         | 2019-07-21 |
| 3          | 4         | 4         | 2019-07-21 |
+------------+-----------+-----------+------------+

Result table:
+------+
| id   |
+------+
| 5    |
| 6    |
+------+


SELECT the viewer_id from table `Views` and change the name to `id` in the result.

Use DISTINCT to avoid duplicates.

Use `GROUP BY viewer_id, view_date` to query the results according to viewers and dates.

The selection criteria is `COUNT(DISTINCT article_id) >= 2`.

Finally, sort the result according to `viewer_id`.
```
<br>

```SQL
CREATE TABLE Views (article_id int, author_id int, viewer_id int, view_date date);

INSERT INTO Views VALUES
(1, 3, 5, '2019-08-01'), (3, 4, 5, '2019-08-01'),
(1, 3, 6, '2019-08-02'), (2, 7, 7, '2019-08-01'),
(2, 7, 6, '2019-08-02'), (4, 7, 1, '2019-07-22'),
(3, 4, 4, '2019-07-21'), (3, 4, 4, '2019-07-21');


SELECT DISTINCT viewer_id AS id
FROM Views
GROUP BY viewer_id, view_date
HAVING COUNT(DISTINCT article_id) >= 2
ORDER BY viewer_id ASC;


SELECT id
FROM (
    SELECT DISTINCT viewer_id AS id,
           COUNT(DISTINCT article_id) AS count_view
    FROM Views
    GROUP BY view_date, viewer_id
) sub
WHERE sub.count_view > 1
ORDER BY sub.id;
```
***