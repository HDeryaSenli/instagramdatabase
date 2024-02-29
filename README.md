# Instagram Database

An Instagram-like database building practice and a brief data analysis using pgSQL. Of course, it doesn't involve all of the features of Instagram. Design and logic of it can be seen by using the link below.

dbdiagram.io was used. Link to database design: [(https://dbdocs.io/halilderyasenli/instagramdb)]

Design and logic of it can be seen by using link below.

### 9 tables are created which are:
* users
* posts
* comments
* likes
* photo_tags
* caption_tags
* hashtags
* hashtags_posts
* followers

## pgSQL Code
dbdiagram exports the code automatically so that is good**
```sql
CREATE TABLE "users" (
  "id" SERIAL PRIMARY KEY,
  "created_at" TIMESTAMP,
  "updated_at" TIMESTAMP,
  "username" VARCHAR(30),
  "bio" VARCHAR(400),
  "avatar" VARCHAR(200),
  "phone" VARCHAR(25),
  "email" VARCHAR(40),
  "password" VARCHAR(50),
  "status" VARCHAR(15)
);

CREATE TABLE "posts" (
  "id" SERIAL PRIMARY KEY,
  "created_at" TIMESTAMP,
  "updated_at" TIMESTAMP,
  "url" VARCHAR(200),
  "user_id" INTEGER,
  "caption" VARCHAR(240),
  "lat" REAL,
  "lng" REAL
);

CREATE TABLE "comments" (
  "id" SERIAL PRIMARY KEY,
  "created_at" TIMESTAMP,
  "updated_at" TIMESTAMP,
  "contents" VARCHAR(240),
  "user_id" INTEGER,
  "post_id" INTEGER
);

CREATE TABLE "likes" (
  "id" SERIAL PRIMARY KEY,
  "created_at" TIMESTAMP,
  "user_id" INTEGER,
  "comment_id" INTEGER,
  "post_id" INTEGER
);

CREATE TABLE "photo_tags" (
  "id" SERIAL PRIMARY KEY,
  "created_at" TIMESTAMP,
  "updated_at" TIMESTAMP,
  "post_id" INTEGER,
  "user_id" INTEGER,
  "x" INTEGER,
  "y" INTEGER
);

CREATE TABLE "caption_tags" (
  "id" SERIAL PRIMARY KEY,
  "created_at" TIMESTAMP,
  "post_id" INTEGER,
  "user_id" INTEGER
);

CREATE TABLE "hashtags" (
  "id" SERIAL PRIMARY KEY,
  "created_at" TIMESTAMP,
  "title" VARCHAR(20)
);

CREATE TABLE "hashtags_posts" (
  "id" SERIAL PRIMARY KEY,
  "hashtag_id" INTEGER,
  "post_id" INTEGER
);

CREATE TABLE "followers" (
  "id" SERIAL PRIMARY KEY,
  "created_at" TIMESTAMP,
  "leader_id" INTEGER,
  "follower_id" INTEGER
);

ALTER TABLE "posts" ADD FOREIGN KEY ("user_id") REFERENCES "users" ("id");

ALTER TABLE "comments" ADD FOREIGN KEY ("user_id") REFERENCES "users" ("id");

ALTER TABLE "comments" ADD FOREIGN KEY ("post_id") REFERENCES "posts" ("id");

ALTER TABLE "likes" ADD FOREIGN KEY ("user_id") REFERENCES "users" ("id");

ALTER TABLE "likes" ADD FOREIGN KEY ("comment_id") REFERENCES "comments" ("id");

ALTER TABLE "likes" ADD FOREIGN KEY ("post_id") REFERENCES "posts" ("id");

ALTER TABLE "photo_tags" ADD FOREIGN KEY ("post_id") REFERENCES "posts" ("id");

ALTER TABLE "photo_tags" ADD FOREIGN KEY ("user_id") REFERENCES "users" ("id");

ALTER TABLE "caption_tags" ADD FOREIGN KEY ("post_id") REFERENCES "posts" ("id");

ALTER TABLE "caption_tags" ADD FOREIGN KEY ("user_id") REFERENCES "users" ("id");

ALTER TABLE "hashtags_posts" ADD FOREIGN KEY ("hashtag_id") REFERENCES "hashtags" ("id");

ALTER TABLE "hashtags_posts" ADD FOREIGN KEY ("post_id") REFERENCES "posts" ("id");

ALTER TABLE "followers" ADD FOREIGN KEY ("leader_id") REFERENCES "users" ("id");

ALTER TABLE "followers" ADD FOREIGN KEY ("follower_id") REFERENCES "users" ("id");

```

## Data Analysis
So I gave this database to ChatGPT and said "What kind of questions should I try to answer in my portfolio about this database?"
Here are the some questions that I could answer from what ChatGPT gave to me:

### Questions and Answers Using pgSQL

```sql
-- Who has the most followers?
SELECT leader_id, COUNT(follower_id) AS follower_count
FROM followers
GROUP BY leader_id
ORDER BY follower_count DESC
LIMIT 1;
```

```sql
-- Which post has the most likes?
SELECT post_id, COUNT(likes.id) AS like_count_per_post
FROM likes
GROUP BY post_id
ORDER BY like_count_per_post DESC
LIMIT 1;
```
```sql
-- Which user has the most likes in total?
SELECT posts.user_id, COUNT(likes.id) AS like_count
FROM posts
LEFT JOIN likes ON posts.id = likes.post_id
GROUP BY posts.user_id
ORDER BY like_count DESC
LIMIT 1;
```
```sql
-- Which post has the most comments?
SELECT post_id, COUNT(comments.id) AS comment_count_per_post
FROM comments
GROUP BY post_id
ORDER BY comment_count_per_post DESC
LIMIT 1;
```
```sql
-- Who are the users who registered to the site last and first?
(
SELECT username
FROM users
ORDER BY created_at DESC
LIMIT 1
)
UNION
(
SELECT username
FROM users
ORDER BY created_at ASC
LIMIT 1
);
```
```sql
-- Which hashtags are most frequently used?
SELECT hashtags.title, count(hashtags_posts.id)
FROM hashtags
JOIN hashtags_posts ON hashtags.id = hashtags_posts.hashtag_id
GROUP BY hashtags.title
ORDER BY count(hashtags_posts.id) DESC;
```
```sql
-- What is the average number of posts per user?
SELECT AVG(post_count) AS average_posts_per_user
FROM 
(
    SELECT users.id, COUNT(posts.id) AS post_count
    FROM users
    JOIN posts ON users.id = posts.user_id
    GROUP BY users.id
) AS post_counts;
```
```sql
-- What is the average number of likes on posts?
SELECT AVG(likes_count) AS avg_likes_per_posts
FROM
(   
    SELECT COUNT(likes.id) AS likes_count
    FROM posts
    JOIN likes ON posts.id = likes.post_id
    GROUP BY posts.id
) AS post_likes;
```
```sql
-- What is the average length of people's bio?
SELECT AVG(bio_length) AS avg_bio_length
FROM 
    (
        SELECT LENGTH(bio) AS bio_length
        FROM users
        GROUP BY id
    ) AS bio_length;

-- OR ANOTHER WAY
SELECT AVG(LENGTH(bio)) AS bio
FROM users;
```
