---
title: mongodb-基本操作（一）
tags: nosql
categories: mongodb
date: 2018-01-01
---

1. 创建数据库
> use [databaseName]

2. 查看所有数据库
> show dbs

3. 向指定集合插入一个文档
> db.[collectionName].insert([document])

4. 查看所有集合
> show collections

5. 查看一个集合中的所有文档
> db.[collectionName].find()

6. 查看一个集合中的一个文档
> db.[collectionName].findOne()

7. 删除集合
> db.[collectionName].drop()

8. 删除数据库
> db.dropDatabase()

9. 帮助
> db.help()
> db.[collectionName].help()