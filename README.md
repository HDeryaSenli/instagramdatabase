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
![image](https://github.com/HDeryaSenli/instagramdatabase/assets/112333951/959edbed-534a-4f14-9a8c-6a325fd059db)

```sql
-- Which post has the most likes?
SELECT post_id, COUNT(id) AS like_count
FROM likes
WHERE post_id IS NOT NULL
GROUP BY post_id
ORDER BY like_count DESC
LIMIT 1;
```
![image](https://github.com/HDeryaSenli/instagramdatabase/assets/112333951/9af166e5-464a-4594-90a6-8237fbf1b5d7)
At first when I didn't use **NOT NULL** db gave me an answer which post_id was **NULL**. So I've added **WHERE post_id IS NOT NULL** constraint.
```sql
-- Which user has the most likes in total?
SELECT posts.user_id, COUNT(likes.id) AS like_count
FROM posts
INNER JOIN likes ON posts.id = likes.post_id
GROUP BY posts.user_id
ORDER BY like_count DESC
LIMIT 1;
```
![image](https://github.com/HDeryaSenli/instagramdatabase/assets/112333951/640f4c18-c7b3-46f9-a077-d28d5a7a5eb2)

```sql
-- Which post has the most comments?
SELECT post_id, COUNT(comments.id) AS comment_count_per_post
FROM comments
GROUP BY post_id
ORDER BY comment_count_per_post DESC
LIMIT 1;
```
![image](https://github.com/HDeryaSenli/instagramdatabase/assets/112333951/c29ec351-6cce-4a3d-9ca4-e87342d3251e)

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
![image](https://github.com/HDeryaSenli/instagramdatabase/assets/112333951/4902198e-d683-44d0-9293-3472bac19358)

```sql
-- Which hashtags are most frequently used?
SELECT hashtags.title, count(hashtags_posts.id)
FROM hashtags
JOIN hashtags_posts ON hashtags.id = hashtags_posts.hashtag_id
GROUP BY hashtags.title
ORDER BY count(hashtags_posts.id) DESC;
```
![image](https://github.com/HDeryaSenli/instagramdatabase/assets/112333951/6c5660e5-368d-4c1e-b3d3-7c697abe8ee5)

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
![image](https://github.com/HDeryaSenli/instagramdatabase/assets/112333951/3d8da7e9-8c0c-4dc3-bef9-a26f5578286d)

```sql
-- What is the average number of likes per post?
SELECT AVG(likes_count) AS avg_likes_per_posts
FROM
(   
    SELECT COUNT(likes.id) AS likes_count
    FROM posts
    JOIN likes ON posts.id = likes.post_id
    GROUP BY posts.id
) AS post_likes;
```
![image](https://github.com/HDeryaSenli/instagramdatabase/assets/112333951/1a772734-4885-45ba-b3ad-6acd3806c402)

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
![image](https://github.com/HDeryaSenli/instagramdatabase/assets/112333951/59894161-9549-4410-bfc9-71e6989689e3)


```sql
--Is there a correlation between the use of specific hashtags and post engagement metrics?
SELECT hashtags.title,
       COUNT(posts.id) AS total_posts,
       COUNT(DISTINCT likes.id) AS total_likes,
       COUNT(DISTINCT comments.id) AS total_comments
FROM hashtags
JOIN hashtags_posts ON hashtags.id = hashtags_posts.hashtag_id
JOIN posts ON hashtags_posts.post_id = posts.id
JOIN likes ON posts.id = likes.post_id
JOIN comments ON posts.id = comments.post_id
GROUP BY hashtags.title;

--Honestly, after this query I find it much easier to download results and work on Excel for basic math because there are only 187 results.
Results are looking like below photos:
```
![image](https://github.com/HDeryaSenli/instagramdatabase/assets/112333951/48c42c2a-dd1c-4655-a640-c36aa257c8f8)

I’ll do total_posts / total likes and total_posts / total comments to see likes per post and comment per post regarding that hashtag.
It looks like this:
![image](https://github.com/HDeryaSenli/instagramdatabase/assets/112333951/adfff615-f767-4830-b074-619df4b50aea)

Now I am going to multiply them . One can think like now the result is a coefficient. The higher it is the better our hashtag title.
Results are below:
![image](https://github.com/HDeryaSenli/instagramdatabase/assets/112333951/ee138877-6c47-4654-9156-1816ba6c3e33)

So, I’ll just order them from highest to lowest. Bigger the number, better.
![image](https://github.com/HDeryaSenli/instagramdatabase/assets/112333951/715d0604-4acc-4aba-8964-bb58d6d696bb)

Even though there are no huge differences, looks like “carissa” have the biggest impact regarding the popularity of the post.

-> 22705 posts used the hashtag “carissa”.

-> Likes per post is 5,2 and comments per post is 29,03.






