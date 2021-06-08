## Comments table 

```sql
CREATE TABLE comments (
  id SERIAL PRIMARY KEY,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP, 
  parent_comment INTEGER REFERENCES comments(id) DEFAULT NULL,
  content VARCHAR(240) NOT NULL
);
```

## Query 

```sql
WITH RECURSIVE replies(comment_id, parent_comment, depth) AS (
    SELECT id AS comment_id, parent_comment, 1 AS depth
    FROM comments
    WHERE id = 1
  UNION
    SELECT comments.id as comment_id, comments.parent_comment, depth + 1
    FROM comments
    JOIN replies ON replies.comment_id = comments.parent_comment
    WHERE depth < 4 -- 👈 Assuming the depth is 3
)
SELECT comments.content
FROM replies
JOIN comments ON comments.id = replies.comment_id
WHERE depth > 1; -- since we do not want the the main comment itself 
```
