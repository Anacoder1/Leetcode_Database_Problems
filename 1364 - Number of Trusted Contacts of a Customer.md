```
Table: Customers

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| customer_id   | int     |
| customer_name | varchar |
| email         | varchar |
+---------------+---------+

customer_id is the primary key for this table.

Each row of this table contains the name and the email of a customer of an online shop.


Table: Contacts

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| user_id       | id      |
| contact_name  | varchar |
| contact_email | varchar |
+---------------+---------+

(user_id, contact_email) is the primary key for this table.

Each row of this table contains the name and email of one contact of customer with user_id.

This table contains information about people each customer trust.
The contact may or may not exist in the Customers table.


Table: Invoices

+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| invoice_id   | int     |
| price        | int     |
| user_id      | int     |
+--------------+---------+

invoice_id is the primary key for this table.

Each row of this table indicates that user_id has an invoice with invoice_id and a price.


Write an SQL query to find the following for each invoice_id:

    customer_name: The name of the customer the invoice is related to.

    price: The price of the invoice.
    
    contacts_cnt: The number of contacts related to the customer.
    
    trusted_contacts_cnt: The number of contacts related to the customer and at the same time they are customers to the shop. (i.e His/Her email exists in the Customers table.)

Order the result table by invoice_id.

The query result format is in the following example:

Customers table:
+-------------+---------------+--------------------+
| customer_id | customer_name | email              |
+-------------+---------------+--------------------+
| 1           | Alice         | alice@leetcode.com |
| 2           | Bob           | bob@leetcode.com   |
| 13          | John          | john@leetcode.com  |
| 6           | Alex          | alex@leetcode.com  |
+-------------+---------------+--------------------+

Contacts table:
+-------------+--------------+--------------------+
| user_id     | contact_name | contact_email      |
+-------------+--------------+--------------------+
| 1           | Bob          | bob@leetcode.com   |
| 1           | John         | john@leetcode.com  |
| 1           | Jal          | jal@leetcode.com   |
| 2           | Omar         | omar@leetcode.com  |
| 2           | Meir         | meir@leetcode.com  |
| 6           | Alice        | alice@leetcode.com |
+-------------+--------------+--------------------+

Invoices table:
+------------+-------+---------+
| invoice_id | price | user_id |
+------------+-------+---------+
| 77         | 100   | 1       |
| 88         | 200   | 1       |
| 99         | 300   | 2       |
| 66         | 400   | 2       |
| 55         | 500   | 13      |
| 44         | 60    | 6       |
+------------+-------+---------+

Result table:
+------------+---------------+-------+--------------+----------------------+
| invoice_id | customer_name | price | contacts_cnt | trusted_contacts_cnt |
+------------+---------------+-------+--------------+----------------------+
| 44         | Alex          | 60    | 1            | 1                    |
| 55         | John          | 500   | 0            | 0                    |
| 66         | Bob           | 400   | 2            | 0                    |
| 77         | Alice         | 100   | 3            | 2                    |
| 88         | Alice         | 200   | 3            | 2                    |
| 99         | Bob           | 300   | 2            | 0                    |
+------------+---------------+-------+--------------+----------------------+

Alice has three contacts, two of them are trusted contacts (Bob and John).

Bob has two contacts, none of them is a trusted contact.

Alex has one contact and it is a trusted contact (Alice).

John doesn't have any contacts.


The fields invoice_id, customer_name and price in the result table can be 
obtained by joining tables Invoices and Customers.

For the fields contacts_cnt and trusted_contacts_cnt, join tables Contacts and Customers.

The field contacts_cnt is the no. of entries in Contacts for each user_id.

The field trusted_contacts_cnt is the no. of entries in table Contacts for each user_id such that contact_name and contact_email are in table Customers.
```
<br>

```SQL
CREATE TABLE Customers (customer_id int, customer_name varchar(10), email varchar(32));
CREATE TABLE Contacts (user_id int, contact_name varchar(10), contact_email varchar(32));
CREATE TABLE Invoices (invoice_id int, price int, user_id int);

INSERT INTO Customers VALUES
(1, 'Alice', 'alice@leetcode.com'), (2, 'Bob', 'bob@leetcode.com'),
(13, 'John', 'john@leetcode.com'), (6, 'Alex', 'alex@leetcode.com');

INSERT INTO Contacts VALUES
(1, 'Bob', 'bob@leetcode.com'), (1, 'John', 'john@leetcode.com'),
(1, 'Jal', 'jal@leetcode.com'), (2, 'Omar', 'omar@leetcode.com'),
(2, 'Meir', 'meir@leetcode.com'), (6, 'Alice', 'alice@leetcode.com');

INSERT INTO Invoices VALUES
(77, 100, 1), (88, 200, 1), (99, 300, 2),
(66, 400, 2), (55, 500, 13), (44, 60, 6);


SELECT T1.invoice_id, T1.customer_name, T1.price,
IF(T2.contacts_cnt IS NULL, 0, contacts_cnt) AS contacts_cnt,
IF(T2.trusted_contacts_cnt IS NULL, 0,  trusted_contacts_cnt) AS trusted_contacts_cnt
FROM (
    SELECT I.invoice_id, C1.customer_name, I.price, I.user_id
    FROM Invoices I LEFT JOIN Customers C1
    ON I.user_id = C1.customer_id
) T1
LEFT JOIN (
    SELECT Co.user_id, COUNT(*) AS contacts_cnt, SUM(IF(C2.customer_name IS NULL, 0, 1)) AS trusted_contacts_cnt
    FROM Contacts Co LEFT JOIN Customers C2
    ON Co.contact_email = C2.email
    GROUP BY Co.user_id
) T2
ON T1.user_id = T2.user_id
ORDER BY T1.invoice_id ASC;


SELECT invoice_id, C0.customer_name, price,
COUNT(Co.contact_email) AS contacts_cnt,
COUNT(C2.email) AS trusted_contacts_cnt
FROM Invoices I
INNER JOIN Customers C0 ON I.user_id = C0.customer_id
LEFT OUTER JOIN Contacts Co ON C0.customer_id = Co.user_id
LEFT OUTER JOIN Customers C2 ON C2.email = Co.contact_email
GROUP BY invoice_id, C0.customer_name, price
ORDER BY invoice_id;
```
***