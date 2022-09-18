```
Table: Experiments

+-----------------+------+
| Column Name     | Type |
+-----------------+------+
| experiment_id   | int  |
| platform        | enum |
| experiment_name | enum |
+-----------------+------+

experiment_id is the primary key for this table.

platform is an enum with one of the values ('Android', 'IOS', 'Web').

experiment_name is an enum with one of the values ('Reading', 'Sports', 'Programming').

This table contains information about the ID of an experiment done with a random person, the platform used to do the experiment, and the name of the experiment.


Write a SQL query to report the number of experiments done on each of the three platforms for each of the three given experiments. 

Notice that all the pairs of (platform, experiment) should be included in the output including the pairs with zero experiments.

Return the result table in any order.

The query result format is in the following example.

Example 1:

Input:
Experiments table:
+---------------+----------+-----------------+
| experiment_id | platform | experiment_name |
+---------------+----------+-----------------+
| 4             | IOS      | Programming     |
| 13            | IOS      | Sports          |
| 14            | Android  | Reading         |
| 8             | Web      | Reading         |
| 12            | Web      | Reading         |
| 18            | Web      | Programming     |
+---------------+----------+-----------------+

Output: 
+----------+-----------------+-----------------+
| platform | experiment_name | num_experiments |
+----------+-----------------+-----------------+
| Android  | Reading         | 1               |
| Android  | Sports          | 0               |
| Android  | Programming     | 0               |
| IOS      | Reading         | 0               |
| IOS      | Sports          | 1               |
| IOS      | Programming     | 1               |
| Web      | Reading         | 2               |
| Web      | Sports          | 0               |
| Web      | Programming     | 1               |
+----------+-----------------+-----------------+

Explanation: 
On the platform "Android", we had only one "Reading" experiment.
On the platform "IOS", we had one "Sports" experiment and one "Programming" experiment.
On the platform "Web", we had two "Reading" experiments and one "Programming" experiment.


Use UNION ALL and CROSS JOIN to get the crossed combinations.

Then use LEFT JOIN to count the number of experiments.
```
<br>

```SQL
CREATE TABLE Experiments (experiment_id int, platform enum('Android', 'IOS', 'Web'), experiment_name enum('Reading', 'Sports', 'Programming'),
    PRIMARY KEY (experiment_id));
    
INSERT INTO Experiments VALUES
(4, 'IOS', 'Programming'), (13, 'IOS', 'Sports'),
(14, 'Android', 'Reading'), (8, 'Web', 'Reading'),
(12, 'Web', 'Reading'), (18, 'Web', 'Programming');


SELECT P.platform, N.experiment_name, COUNT(E.platform) AS num_experiments
FROM (
    SELECT 'Android' AS platform
    UNION ALL
    SELECT 'IOS' AS platform
    UNION ALL
    SELECT 'Web' AS platform
) AS P
CROSS JOIN (
    SELECT 'Reading' AS experiment_name
    UNION ALL
    SELECT 'Sports' AS experiment_name
    UNION ALL
    SELECT 'Programming' AS experiment_name
) AS N
LEFT JOIN Experiments E USING (platform, experiment_name)
GROUP BY P.platform, N.experiment_name
ORDER BY P.platform;


SELECT platform, experiment_name, IFNULL(num_experiments, 0) AS num_experiments
FROM (
    SELECT 'Android' AS platform
    UNION
    SELECT 'IOS' AS platform
    UNION
    SELECT 'Web' AS platform
) A
CROSS JOIN (
    SELECT 'Reading' AS experiment_name
    UNION
    SELECT 'Sports' AS experiment_name
    UNION
    SELECT 'Programming' AS experiment_name
) B
LEFT JOIN (
    SELECT platform, experiment_name, COUNT(*) num_experiments
    FROM Experiments
    GROUP BY 1, 2
) C
USING (platform, experiment_name)
ORDER BY 1, 2;
```
***