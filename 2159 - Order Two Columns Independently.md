```
(couldn't find the question description)
```
<br>

> Solution taken from [here](https://github.com/kamyu104/LeetCode-Solutions/blob/master/MySQL/order-two-columns-independently.sql)
<br>

```SQL
SELECT first_col, second_col
FROM (
    SELECT first_col, ROW_NUMBER() OVER (ORDER BY first_col ASC) AS rnk
    FROM Data
) A
INNER JOIN (
    SELECT second_col, ROW_NUMBER() OVER (ORDER BY second_col DESC) AS rnk
    FROM Data
) B
ON A.rnk = B.rnk;
```
***