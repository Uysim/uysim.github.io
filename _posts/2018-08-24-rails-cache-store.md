---
layout: post
title: "Rails Cache Stores to consider"
description: "List of Rails cache stores for make decision for your project"
img: rails-cache-stores-to-consider.jpg
tags: [Rails]
---

# Rails Cache Stores to consider

Rails app performance is a serious issue for mid-level startup. The big mess in project develops quality block you away from improving you app speed. To me,  a short-term solution is caching. The method I prefer to use for existing project when the client wants to improve performance immediately while project scope is out of your hand.

As a Rails developer, we can see how to develop friendly of Rails cache. Along with this we also need to make the decision on our cache store as well. This is why I list the Rails cache store below that help you to make a decision for your rails project

### 1. Null Store
This null store is no cache at all. This store mostly uses for development and testing which won't store any cache by default.

**Configuration**
```
config.cache_store = :null_store
```

### 2. Memory Store
Memory Store will store your cache in the memory a long with Ruby process. This kind of store is popular amount basic Ruby On Rails developer. It is easy to config and work with

**Configuration**
```
config.cache_store = :memory_store
```

### 3. File Store
For the file store is not so popular. It is not easy to see every day in the normal project. Normally it is used when we have a lot of big caching and we have a small server which less of RAM that needs to replace by file. This kind of store makes your cache slower according to your hard disk speed.

**Configuration**
```
config.cache_store = :file_store
```

### 4. Mem Cache Store
Rails store cache by using memcached servers. By this store, you need to integrate with your `gem Dalli` as well. Memcached is a high-performance server application that will store your data the RAM. That makes it really fast follow the read and write speed of the ream

**Configuration**
```
config.cache_store = :mem_cache_store
```

### 5. Redis Cache Store
The cache will be stored on the **redis server**. Also, you need to integrate with `gem redis` for this cache store. Redis have known as a fast performance dataset that has been used as the database. This store count as the most advanced Rails cache store which is popular for all production application.

**Configuration**
```
config.cache_store = :mem_cache_store
```

**Extra** : I just published my [gem api-response-cache](https://github.com/Uysim/api-response-cache) feel free to try it out for you API. I open for feedback on github.


