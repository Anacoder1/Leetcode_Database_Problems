```
Table: Movies

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| movie_id      | int     |
| title         | varchar |
+---------------+---------+

movie_id is the primary key for this table.

title is the name of the movie.


Table: Users

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| user_id       | int     |
| name          | varchar |
+---------------+---------+

user_id is the primary key for this table.


Table: Movie_Rating

+---------------+---------+
| Column Name   | Type    |
+---------------+---------+
| movie_id      | int     |
| user_id       | int     |
| rating        | int     |
| created_at    | date    |
+---------------+---------+

(movie_id, user_id) is the primary key for this table.

This table contains the rating of a movie by a user in their review.

created_at is the user's review date.


Write the following SQL query:

A) Find the name of the user who has rated the greatest number of the movies.

In case of a tie, return lexicographically smaller user name.

B) Find the movie name with the highest average rating in February 2020.

In case of a tie, return lexicographically smaller movie name..


Query is returned in 2 rows, the query result format is in 
the folowing example:

Movies table:
+-------------+--------------+
| movie_id    |  title       |
+-------------+--------------+
| 1           | Avengers     |
| 2           | Frozen 2     |
| 3           | Joker        |
+-------------+--------------+

Users table:
+-------------+--------------+
| user_id     |  name        |
+-------------+--------------+
| 1           | Daniel       |
| 2           | Monica       |
| 3           | Maria        |
| 4           | James        |
+-------------+--------------+

Movie_Rating table:
+-------------+--------------+--------------+-------------+
| movie_id    | user_id      | rating       | created_at  |
+-------------+--------------+--------------+-------------+
| 1           | 1            | 3            | 2020-01-12  |
| 1           | 2            | 4            | 2020-02-11  |
| 1           | 3            | 2            | 2020-02-12  |
| 1           | 4            | 1            | 2020-01-01  |
| 2           | 1            | 5            | 2020-02-17  |
| 2           | 2            | 2            | 2020-02-01  |
| 2           | 3            | 2            | 2020-03-01  |
| 3           | 1            | 3            | 2020-02-22  |
| 3           | 2            | 4            | 2020-02-25  |
+-------------+--------------+--------------+-------------+

Result table:
+--------------+
| results      |
+--------------+
| Daniel       |
| Frozen 2     |
+--------------+

Daniel and Monica have rated 3 movies ("Avengers", "Frozen 2" and "Joker") but Daniel is smaller lexicographically.

Frozen 2 and Joker have a rating average of 3.5 in February but Frozen 2 is smaller lexicographically.
```
<br>

```SQL
CREATE TABLE Movies (movie_id int, title varchar(32), PRIMARY KEY (movie_id));

CREATE TABLE Users (user_id int, name varchar(32), PRIMARY KEY (user_id));

CREATE TABLE Movie_Rating (
    movie_id int, user_id int, rating int, 
    created_at date, 
    PRIMARY KEY (movie_id, user_id),
    FOREIGN KEY (movie_id) REFERENCES Movies(movie_id),
    FOREIGN KEY (user_id) REFERENCES Users(user_id)
);

INSERT INTO Movies VALUES
(1, 'Avengers'), (2, 'Frozen 2'), (3, 'Joker');

INSERT INTO Users VALUES
(1, 'Daniel'), (2, 'Monica'), (3, 'Maria'), (4, 'James');

INSERT INTO Movie_Rating VALUES
(1, 1, 3, '2020-01-12'), (1, 2, 4, '2020-02-11'),
(1, 3, 2, '2020-02-12'), (1, 4, 1, '2020-01-01'),
(2, 1, 5, '2020-02-17'), (2, 2, 2, '2020-02-01'),
(2, 3, 2, '2020-03-01'), (3, 1, 3, '2020-02-22'),
(3, 2, 4, '2020-02-25');


(
    SELECT name results
    FROM Movie_Rating NATURAL JOIN Users
    GROUP BY Users.user_id
    ORDER BY COUNT(*) DESC, name ASC
    LIMIT 1
)
UNION
(
    SELECT Movies.title results
    FROM Movie_Rating NATURAL JOIN Movies
    WHERE MONTH(Movie_Rating.created_at) = '2'
    GROUP BY Movies.movie_id
    ORDER BY AVG(Movie_Rating.rating) DESC, Movies.title ASC
    LIMIT 1
);


(
    SELECT name results
    FROM Users LEFT JOIN Movie_Rating USING (user_id)
    GROUP BY user_id
    ORDER BY COUNT(Movie_Rating.rating) DESC, Users.name ASC
    LIMIT 1
)
UNION
(
    SELECT Movies.title
    FROM Movies LEFT JOIN Movie_Rating USING (movie_id)
    WHERE LEFT(Movie_Rating.created_at, 7) = '2020-02'
    GROUP BY movie_id
    ORDER BY AVG(Movie_Rating.rating) DESC, Movies.title ASC
    LIMIT 1
);
```
***