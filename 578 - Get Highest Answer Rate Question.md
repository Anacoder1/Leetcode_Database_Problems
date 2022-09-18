```
Get the highest answer rate question from a table `survey_log` with these columns: id, action, question_id, answer_id, q_num, timestamp.

id means user id;
action has these kind of values: “show”, “answer”, “skip”;
answer_id is not null when action column is “answer”, while is null for “show” and “skip”;
q_num is the numeral order of the question in current session.


Write a SQL query to identify the question which has the highest answer rate.

Example:

Input:
+------+-----------+--------------+------------+-----------+------------+
| id   | action    | question_id  | answer_id  | q_num     | timestamp  |
+------+-----------+--------------+------------+-----------+------------+
| 5    | show      | 285          | null       | 1         | 123        |
| 5    | answer    | 285          | 124124     | 1         | 124        |
| 5    | show      | 369          | null       | 2         | 125        |
| 5    | skip      | 369          | null       | 2         | 126        |
+------+-----------+--------------+------------+-----------+------------+

Output:
+-------------+
| survey_log  |
+-------------+
|    285      |
+-------------+

Explanation:
Question 285 has answer rate 1/1, while question 369 has 0/1 answer rate, so output 285.

NOTE: The highest answer rate meaning is: answer number’s ratio in show number in the same question.


SELECT `question_id` from table `survey_log` and output the column as `survey_log`,
and ORDER the selected results by the answer rate in DESCENDING order.

To obtain the answer rate, use `COUNT(answer_id) / COUNT(IF('show', 1, 0))`.

Use `LIMIT 1` to select only 1 question with the highest answer rate.
```
<br>

```SQL
CREATE TABLE survey_log (id int, action enum('show', 'answer', 'skip'), question_id int, answer_id int, q_num int, `timestamp` int);

INSERT INTO survey_log VALUES
(5, 'show', 285, null, 1, 123),
(5, 'answer', 285, 124124, 1, 124),
(5, 'show', 369, null, 2, 125),
(5, 'skip', 369, null, 2, 126);


SELECT question_id AS survey_log
FROM survey_log
GROUP BY question_id
ORDER BY COUNT(answer_id) / COUNT(IF(action = 'show', 1, 0)) DESC
LIMIT 1;


SELECT question_id AS survey_log
FROM survey_log
GROUP BY question_id
ORDER BY COUNT(answer_id) / SUM(IF(action = 'show', 1, 0)) DESC
LIMIT 1;
```
***