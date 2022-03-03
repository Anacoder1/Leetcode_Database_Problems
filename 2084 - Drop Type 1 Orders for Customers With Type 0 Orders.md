> The first 2 solutions are from [here](https://github.com/kamyu104/LeetCode-Solutions/blob/master/MySQL/drop-type-1-orders-for-customers-with-type-0-orders.sql), the next 2 are from [here](https://blog.csdn.net/Dream_Weave/article/details/121969069)
<br>

```SQL
SELECT * FROM Orders
WHERE (customer_id, order_type)
IN (
    SELECT customer_id, MIN(order_type)
    FROM Orders
    GROUP BY customer_id
    ORDER BY NULL
);


WITH cte AS (
    SELECT *, RANK() OVER (PARTITION BY customer_id ORDER BY order_type) AS rnk
    FROM Orders
)
SELECT order_id, customer_id, order_type
FROM cte
WHERE rnk = 1;


SELECT DISTINCT A.order_id, A.customer_id, A.order_type
FROM Orders AS A LEFT JOIN Orders AS B
ON A.customer_id = B.customer_id AND A.order_type <> B.order_type
WHERE B.order_type IS NULL OR B.order_type = 1;


WITH T0 AS (SELECT * FROM Orders WHERE order_type = 0)
SELECT * FROM Orders
WHERE order_type = 1 AND customer_id NOT IN 
(SELECT customer_id FROM T0)
UNION ALL
SELECT * FROM T0;
```
***