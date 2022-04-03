```
Given a table Tree, id is identifier of the tree node and p_id is its parent node’s id.

+----+------+
| id | p_id |
+----+------+
| 1  | null |
| 2  | 1    |
| 3  | 1    |
| 4  | 2    |
| 5  | 2    |
+----+------+

Each node in the tree can be one of three types:

- Leaf: if the node is a leaf node.
- Root: if the node is the root of the tree.
- Inner: If the node is neither a leaf node nor a root node.

Write a query to print the node id and the type of the node.

Sort your output by the node id. The result for the above sample is:

+----+------+
| id | Type |
+----+------+
| 1  | Root |
| 2  | Inner|
| 3  | Leaf |
| 4  | Leaf |
| 5  | Leaf |
+----+------+

Explanation

- Node ‘1’ is root node, because its parent node is NULL and it has child node ‘2’ and ‘3’.
- Node ‘2’ is inner node, because it has parent node ‘1’ and child node ‘4’ and ‘5’.
- Node ‘3’, ‘4’ and ‘5’ is Leaf node, because they have parent node and they don’t have child node.
- And here is the image of the sample tree as below:

        1
      /   \
    2       3
  /   \
4       5

NOTE : If there is only one node on the tree, you only need to output its root attributes.


Select the three types of node individually.
Then use UNION to merge the result, and sort the result according to id.

A node is a leaf if and only if it is not any node’s parent and it has a parent.

A node is a root if and only if it does not have a parent. In other words, p_id is null.

A node is an inner node if and only if it is one or more nodes’ parent and it has a parent.

Therefore, to determine a Root, simply use p_id.

To determine a Leaf or Inner, check the current entry’s p_id and whether the current entry’s id is in some entry’s p_id.
```
<br>

```SQL
SELECT id, 'Root' AS Type
FROM Tree
WHERE p_id IS NULL

UNION

SELECT id, 'Leaf' AS Type
FROM Tree
WHERE id NOT IN (
    SELECT DISTINCT p_id
    FROM Tree
    WHERE p_id IS NOT NULL
) AND p_id IS NOT NULL

UNION

SELECT id, 'Inner' AS Type
FROM Tree
WHERE id IN (
    SELECT DISTINCT p_id
    FROM Tree
    WHERE p_id IS NOT NULL
) AND p_id IS NOT NULL
ORDER BY id;
```
***