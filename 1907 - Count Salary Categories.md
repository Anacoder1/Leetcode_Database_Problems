```
Table: Accounts

+-------------+------+
| Column Name | Type |
+-------------+------+
| account_id  | int  |
| income      | int  |
+-------------+------+

account_id is the primary key for this table.

Each row contains information about the monthly income for one bank account.


Write an SQL query to report the number of bank accounts of each salary category.

The salary categories are:

- "Low Salary": All the salaries strictly less than $20000.
- "Average Salary": All the salaries in the inclusive range [$20000, $50000].
- "High Salary": All the salaries strictly greater than $50000.

The result table must contain all three categories.

If there are no accounts in a category, then report 0. 

Return the result table in any order.

The query result format is in the following example.

Accounts table:
+------------+--------+
| account_id | income |
+------------+--------+
| 3          | 108939 |
| 2          | 12747  |
| 8          | 87709  |
| 6          | 91796  |
+------------+--------+

Result table:
+----------------+----------------+
| category       | accounts_count |
+----------------+----------------+
| Low Salary     | 1              |
| Average Salary | 0              |
| High Salary    | 3              |
+----------------+----------------+


First, create T as the UNION of all categories Low Salary, Average Salary and High Salary.

Next, convert the values of income into the corresponding categories.

Finally, use LEFT JOIN to combine the query results and use IFNULL to return 0 for categories with no accounts.
```
<br>

```SQL
CREATE TABLE Accounts (account_id int, income int);

INSERT INTO Accounts VALUES
(3, 108939), (2, 12747), (8, 87709), (6, 91796);


SELECT T.category, IFNULL(A.cnt, 0) AS accounts_count
FROM (
    SELECT 'Low Salary' category
    UNION
    SELECT 'Average Salary'
    UNION
    SELECT 'High Salary'
) AS T
LEFT JOIN (
    SELECT
    CASE
    WHEN income < 20000 THEN 'Low Salary'
    WHEN income > 50000 THEN 'High Salary'
    ELSE 'Average Salary'
    END AS category,
    COUNT(1) AS cnt
    FROM Accounts
    GROUP BY 1
) AS A
ON T.category = A.category;


SELECT 'Low Salary' AS category, COUNT(*) AS accounts_count
FROM Accounts
WHERE income < 20000
UNION
SELECT 'Average Salary' AS category, COUNT(*) AS accounts_count
FROM Accounts
WHERE 20000 <= income AND income <= 50000
UNION
SELECT 'High Salary' AS category, COUNT(*) AS accounts_count
FROM Accounts
WHERE 50000 < income;
```
***