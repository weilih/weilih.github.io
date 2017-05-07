---
title: "Prettify Nested IDs with Composite Keys"
date: 2017-05-07T22:00:00+08:00
comments: true
---

:warning: ***Examples are written in PostgreSQL DDL and PL/SQL.***

Usually this is how you create tables relations in RESTful resources on a typical Web application which uses **Surrogate Key** as primary key.

```sql
CREATE DATABASE blog;
CREATE TABLE posts (id SERIAL PRIMARY KEY, title VARCHAR(255), body TEXT);
CREATE TABLE comments (id SERIAL PRIMARY KEY, body TEXT, post_id INTEGER REFERENCES posts(id));
```

```cmd
blog=# INSERT INTO posts(title, body) VALUES('hello', 'hello world');
blog=# INSERT INTO comments(post_id, body) VALUES(1, 'FIRST!');

blog=# INSERT INTO posts(title, body) VALUES('SSS', '2nd Sys. Syndrome');
blog=# INSERT INTO comments(post_id, body) VALUES(2, '1st!');
blog=# INSERT INTO comments(post_id, body) VALUES(2, '2nd!');
blog=# SELECT post_id, id, body FROM comments;
 post_id | id |  body  
---------+----+--------
       1 |  1 | FIRST!
       2 |  2 | 1st!
       2 |  3 | 2nd!
```

## Composite Key

Simple, right? Nothing special.
But what if, for some reason, you want `comment.id` to start from `'1'` on every post entry?
This is when you might want to use **Composite Key**! A composite key consist of more than one attribute to uniquely identify an entity occurrence.

Here's an _**naive**_ approach to assign next `comment.id` using database TRIGGER.

```sql
DROP TABLE comments;
CREATE TABLE comments (id INTEGER, body TEXT, post_id INTEGER REFERENCES posts(id));
ALTER TABLE comments ADD PRIMARY KEY (post_id, id);

CREATE OR REPLACE FUNCTION set_comment_id_seq()
RETURNS TRIGGER AS $$
DECLARE
  current_value INTEGER;
BEGIN
  SELECT COALESCE(MAX(id), 0) INTO current_value FROM comments WHERE post_id = NEW.post_id;
  NEW.id := current_value + 1;
RETURN NEW;
END;
$$ LANGUAGE plpqsql;

CREATE TRIGGER set_comment_id_trg BEFORE INSERT ON comments FOR EACH ROW EXECUTE PROCEDURE set_comment_id_seq();
```

```cmd
blog=# INSERT INTO comments(post_id, body) VALUES(1, 'FIRST!');
blog=# INSERT INTO comments(post_id, body) VALUES(2, '1st!');
blog=# INSERT INTO comments(post_id, body) VALUES(2, '2nd!');
blog=# SELECT post_id, id, body FROM comments;
 post_id | id |  body  
---------+----+--------
       1 |  1 | FIRST!
       2 |  1 | 1st!
       2 |  2 | 2nd!
```

## Improvement Idea
The _**naive**_ approach above would rollback when latest comment is deleted. To solve this, we could use a table to store/update 'next_value' of each table when insert event triggered.

## Afterword
Does it worth the effort to use Composite Key over Surrogate Key? Usually not.
Things get complicated if table 'comments' has a child table because now it requires to store 2 columns of foreign keys.
In short, Surrogate Key is always the safe bet.


### References
- [PostgreSQL: CREATE FUNCTION](https://www.postgresql.org/docs/current/static/sql-createfunction.html)
- [PostgreSQL: CREATE TRIGGER](https://www.postgresql.org/docs/current/static/sql-createtrigger.html)
