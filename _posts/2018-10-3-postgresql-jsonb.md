---
layout: post
title: "PostgreSQL JSONB"
description: "Introduce to Postgres JSONB that developer need to know"
img: postgresql-jsonb.png
tags: [PostgreSQL, Database]
---

I believe if you are in Postgres Community, you will know about powerful PSQL is. I have worked across multiple databases include PostgreSQL, MySQL, and Mongo DB buy recently I take advantage of PostgreSQL JSONB that's why I decided to craft this blog.

### What is Postgres JSONB
JONB is one of the data types in PostgreSQL that allow saving JSON object into it. It also has features to allow you to select query and update it later with fast performance.

### Work with JSONB?
Below is just brief of basic usage of JSONB

##### 1. Create Table with JSONB
```
CREATE TABLE users (
    id serial PRIMARY KEY,
    meta_data jsonb
);
````

##### 2. Add JSONB to table
```
ALTER TABLE users ADD COLUMN meta_data jsonb;
```
##### 3. Insert JSONB
```
INSERT INTO users VALUES ( 1, '{ "name": "User Name", "tags": ["tag 1", "tag 2", "tag 2"] }');
```


##### 4. Select Query
```
SELECT * FROM users WHERE users->name ILIKE 'testing';
```

### Performance
When I begin working with PSQL JSONB, I was so worried about performance. Even so, I still decide to use its base on some research on few blogs online. After feature rollout, it didn't disappoint me. Postgres works really great. Also, thank  RDS which provide high-performance database server.

### Personal Experience
Recently, I've worked with few projects that allow flexible of data. That data allows for dynamic fields and data type. JSONB is really effective in that case as JSON is a flexible data type.
Also, it is useful for the backend that allows the front to save any kind of data. When the client pushes a JSON object to the server we get the whole object and save to database immediately without taking care of extract the data. We can serve back to the client with the whole object the same form they push. We can query the JSONB column in case we need for analytic or other business logic use.

### Why Postgres JSONB?
Before JSONB of Postgres, we can see a lot of developers need to roll out the solution with NoSQL like MongoDB or stringify JSON to save into text field. Which is hard to scale and affect the performance for long-term use.
But nowhere is the solution. With PSQL JSONB that can ensure of performance, accountable and scalable.



