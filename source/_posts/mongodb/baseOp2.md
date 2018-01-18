---
title: mongodb-文档更新
tags: nosql
categories: mongodb
date: 2018-01-18
---

## 更新

### 强硬的替换式更新

新文档会替换掉旧文档，如果旧文档已存在，则报主键冲突错误。
```
db.(collectionName).update({条件},{新文档})
```

### 插入或更新方式
```
db.(collectionName).update(查询器,修改器,true)
```
<!--more-->
### 具体语法
```
db.(collectionName).update({查询器},{修改器}[,是否插入,是否批量更新]);
是否插入：如果查询器没有查到文档，插入修改器内容
是否批量更新一般和$一起使用
```

### 练习
1. 创建并加入文档 **updateTest**
    use updateTest 
    db.updateTest.insert({name:'aaa',age:25,gendar:'man'})
    db.updateTest.insert({name:'bbb',age:22,gendar:'woman'})
2. 将name为aaa的用户修改name为ccc,age:10
    db.updateTest.update({name:'aaa'},{name:'ccc',age:10})
3. 查看
    db.updateTest.find()
    -
    { "_id" : ObjectId("5a60568d19b87d79059ce174"), "name" : "ccc", "age" : 10 }
    { "_id" : ObjectId("5a60568e19b87d79059ce175"), "name" : "bbb", "age" : 22, "gendar" : "woman" }
    -

## 局部更新

### $set
语法| 案例
--|--
{$set:{field:value}} | {$set:{age:25}}

#### 练习
1. 将**updateTest**中name为bbb的文档中gendar更新为man
    db.updateTest.update({name:'bbb'},{$set:{gendar:'man'}})
2. 查看是否成功
    db.updateTest.find()
    -
    { "_id" : ObjectId("5a60568e19b87d79059ce175"), "name" : "bbb", "age" : 22, "gendar" : "man" }
    -
