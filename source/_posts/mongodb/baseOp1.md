---
title: mongodb-基本操作（一）
tags: mongodb
categories: nosql
date: 2018-01-15
---

### 创建数据库
```
use [databaseName]
```

### 查看所有数据库
```
show dbs
```
<!--more-->
### 向指定集合插入一个文档
```
db.[collectionName].insert([document])
db.[collectionName].save([document])

save 和 insert的不同：
    当_id相同时，insert会报出key相同的错误，save会将原数据更新
```

### 查看所有集合
```
show collections
```
### 删除几个中的文档
```
db.[collectionName].remove(query);
```

### 查看集合中的文档
```
db.[collectionName].find([condition])
```

### 查看一个集合中的一个文档
```
db.[collectionName].findOne([condition])
```

### 删除集合
```
db.[collectionName].drop()
```

### 删除数据库
```
db.dropDatabase()
```

### 帮助
```
db.help()
db.[collectionName].help()
```

### 练习

1. 创建数据库【blog】
use blog
2. 加入文档
    db.blog.insert({name:'i7baoz',age:25,gendar:'man',msg:'hello World'})
    db.blog.save({name:'gugu',age:23,gendar:'women',msg:'beautiful girl'})
3. 查看所有文档
    db.blog.find()
    -
    { "_id" : ObjectId("5a60472219b87d79059ce16f"), "name" : "i7baoz", "age" : 25, "gendar" : "man", "msg" : "hello World" }
    { "_id" : ObjectId("5a60475e19b87d79059ce170"), "name" : "gugu", "age" : 23, "gendar" : "women", "msg" : "beautiful girl" }
    -
4. 查看一个文档
    db.blog.findOne();
    -
    {
        "_id" : ObjectId("5a60472219b87d79059ce16f"),
        "name" : "i7baoz",
        "age" : 25,
        "gendar" : "man",
        "msg" : "hello World"
    }
    -
5. 删除name=i7baoz的文档
    db.blog.remove({name:'i7baoz'})
6. 删除数据库
    db.dropDatabase();
7. 查看所有数据库
    show dbs