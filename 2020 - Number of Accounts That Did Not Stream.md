```
(couldn't find the question anywhere)
```
<br>

> Solution taken from [here](https://github.com/kamyu104/LeetCode-Solutions/blob/master/MySQL/number-of-accounts-that-did-not-stream.sql)
<br>

```SQL
WITH bought_cte AS (
    SELECT account_id
    FROM Subscriptions
    WHERE end_date >= '2021-01-01'
),
no_stream_cte AS (
    SELECT account_id
    FROM Streams
    WHERE stream_date < '2021-01-01'
)
SELECT COUNT(B.account_id) AS accounts_count
FROM bought_cte B INNER JOIN no_stream_cte N
ON B.account_id = N.account_id;
```
***