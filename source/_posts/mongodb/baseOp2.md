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
```
    { "_id" : ObjectId("5a60568d19b87d79059ce174"), "name" : "ccc", "age" : 10 }
    { "_id" : ObjectId("5a60568e19b87d79059ce175"), "name" : "bbb", "age" : 22, "gendar" : "woman" }
```

## 局部更新

### $set
语法| 案例
--|--
{$set:{field:value}} | {$set:{age:25}}

**如果存在键就进行修改，不存在就添加**

#### 修改--练习

1. 将**updateTest**中name为bbb的文档中gendar更新为man
    db.updateTest.update({name:'bbb'},{$set:{gendar:'man'}})
2. 查看是否成功
    db.updateTest.find()
```
    { "_id" : ObjectId("5a60568e19b87d79059ce175"), "name" : "bbb", "age" : 22, "gendar" : "man" }
```

#### 添加--练习

1. 将**updateTest**中name为bbb的文档添加身高字段height,值为175cm
   db.updateTest.update({name:'bbb'},{$set:{height:175}})
2. 查看是否成功
    db.updateTest.find({name:'bbb'})
```
    { "_id" : ObjectId("5a60568e19b87d79059ce175"), "name" : "bbb", "age" : 22, "gendar" : "man", "height" : 175 }
```

### $inc

为指定键对应的数字类型值进行加减操作
**键值必须是数字类型**

语法|案例
--|--
{$inc:{field:value}}|{$inc:{height:5}}

#### 加法--练习
1. 将**updateTest**中name为bbb的文档身高加5
    db.updateTest.update({name:'bbb'},{$inc:{height:5}})
2. 查看
    db.updateTest.find({name:'bbb'})
```
    { "_id" : ObjectId("5a60568e19b87d79059ce175"), "name" : "bbb", "age" : 22, "gendar" : "man", "height" : 180 }
```

#### 减法--练习
1. 将**updateTest**中name为bbb的文档身高减去10
    db.updateTest.update({name:'bbb'},{$inc:{height:-10}})
2. 查看
    db.updateTest.find({name:'bbb'})
```
    { "_id" : ObjectId("5a60568e19b87d79059ce175"), "name" : "bbb", "age" : 22, "gendar" : "man", "height" : 170 }
```

### $unset
删除指定的键

语法|案例
--|--
{$unset:{field:value}} | {$unset:{age:20}}

#### 练习
1. 删除bbb的age键
    db.updateTest.update({name:'bbb'},{$unset:{age:20}})
2. db.updateTest.find({name:'bbb'})
```
    { "_id" : ObjectId("5a60568e19b87d79059ce175"), "name" : "bbb", "gendar" : "man", "height" : 170 }
```

### $push

语法|案例
--|--
{$push:{field:value}} | 

1. 如果指定键不存在，则创建数据类型键值对
2. 如果指定键的值是数组，则追加新的值
3. 如果指定键的值不是数组则中断当前操作

#### 练习
1. 给bbb添加游戏games,先添加lol
    db.updateTest.update({name:'bbb'},{$push:{game:'lol'}})
```
    { "_id" : ObjectId("5a60568e19b87d79059ce175"), "name" : "bbb", "gendar" : "man", "height" : 170, "game" : [ "lol" ] }
```
2. 再添加CF
    db.updateTest.update({name:'bbb'},{$push:{game:'cf'}})
```
    { "_id" : ObjectId("5a60568e19b87d79059ce175"), "name" : "bbb", "gendar" : "man", "height" : 170, "game" : [ "lol", "cf" ] }
```
3. 错误示范
    db.updateTest.update({name:'bbb'},{$push:{gendar:'err'}})
    错误信息：gendar 键值不是数组
     "errmsg" : "The field 'gendar' must be an array but is of type string in document {_id: ObjectId('5a60568e19b87d79059ce175')}"

### $pushAll

与$push相似，批量添加数组数据

#### 练习
1. bbb一次性添加游戏：coc，pubg，h1z1
    db.updateTest.update({name:'bbb'},{$pushAll:{game:['coc','pubg','h1z1']}})
```
{ "_id" : ObjectId("5a60568e19b87d79059ce175"), "name" : "bbb", "gendar" : "man", "height" : 170, "game" : [ "lol", "cf", "coc", "pubg", "h1z1" ] }
```

### $addToSet

向目标数组添加值，目标数组不存在则创建目标key
    db.updateTest.update({name:'bbb'},{$addToSet:{game1:'QQ飞车'}})
```
{ "_id" : ObjectId("5a60568e19b87d79059ce175"), "name" : "bbb", "gendar" : "man", "height" : 170, "game" : [ "lol", "cf", "coc", "pubg", "h1z1", "QQ飞车" ], "game1" : [ "QQ飞车" ] }
```


    db.updateTest.update({name:'bbb'},{$addToSet:{game2:['穿越火线','穿越土线']}})

```
    { "_id" : ObjectId("5a60568e19b87d79059ce175"), "name" : "bbb", "gendar" : "man", "height" : 170, "game" : [ "lol", "cf", "coc", "pubg", "h1z1", "QQ飞车" ], "game1" : [ "QQ飞车" ], "game2" : [ [ "穿越火线", "穿越土线" ] ] }
```

### $pop

语法|案例
--|--
{$pop:{field:1/-1}} | 

从指定数组删除一个值，1删除最后一个值，-1删除第一个值

db.updateTest.update({name:'bbb'},{$pop:{game:1}})

```
{ "_id" : ObjectId("5a60568e19b87d79059ce175"), "name" : "bbb", "gendar" : "man", "height" : 170, "game" : [ "lol", "cf", "coc", "pubg", "h1z1" ], "game1" : [ "QQ飞车" ], "game2" : [ [ "穿越火线", "穿越土线" ] ] }
```

### $pull

语法|案例
--|--
{$pull:{field:value}} |

删除数组中一个被指定的值

db.updateTest.update({name:'bbb'},{$pull:{game:'coc'}}) 

```
{ "_id" : ObjectId("5a60568e19b87d79059ce175"), "name" : "bbb", "gendar" : "man", "height" : 170, "game" : [ "lol", "cf", "pubg", "h1z1" ], "game1" : [ "QQ飞车" ], "game2" : [ [ "穿越火线", "穿越土线" ] ] }
```
### $pullAll

语法|案例
--|--
{$pullAll:{field:array}} |

一次性删除数组中多个指定的值

db.updateTest.update({name:'bbb'},{$pullAll:{game:['lol','cf']}})

### 数组定位器$

如果数组中有多个值，我们只想对其中一部分进行操作，就要用'$'

准备文档


db.test.insert({name:'abc',age:25,books:[{type:'js',name:'extjs4'},{type:'js',name:'jquery'},{type:'db',name:'mongodb'}]})

查看该文档
db.test.find({name:'abc','books.type':'js'}) // 注意 books.type 必须用引号引起来

现在我们要把type等于js的文档增加一个相同的作者author:'zhang'
db.test.update({"books.type":'js'},{$set:{'books.$.author':'zhang'}})

再次查看该文档
db.test.find({name:'abc','books.type':'js'})

```
{ "_id" : ObjectId("5a6588231ecc368cc1fa84e9"), "name" : "abc", "age" : 25, "books" : [ { "type" : "js", "name" : "extjs4", "author" : "zhang" }, { "type" : "js", "name" : "jquery" }, { "type" : "db", "name" : "mongodb" } ] }
```

> 这里只修改了一个 ？ 可能是版本的问题？ 
> 后续会解决 ~！~

### $addToSet和$each结合使用


准备数据
db.test.insert({name:'zhang',books:['js','jquery']})
检查数据
db.test.find({name:'zhang'})
将 java ,sql server加入到books中
db.test.update({name:'zhang'},{$addToSet:{books:{$each:['java','sql server']}}})
