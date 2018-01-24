---
title: mongodb-文档查询
tags: nosql
categories: mongodb
date: 2018-01-24
---

### 数据准备
```
1. use test
2. db.user.insert({name:'zhang',age:25,games:['lol','coc','cf','pubg'],books:[{type:'js',name:'jQuery'},{type:'db',name:'oracle'},{type:'db',name:'mysql'}]})
3. db.user.insert({name:'gu',age:22,games:['phone','lol'],books:[{type:'js',name:'jQuery'},{type:'server',name:'java'},{type:'db',name:'mysql'}]})
4. db.user.insert({name:'bao',age:12,games:['coc','lol'],books:[{type:'db',name:'oracle'},{type:'server',name:'java'},{type:'db',name:'mysql'}]})
```
### 格式
db.collectionName.find(条件,{显示的键})
显示键 通过 key:0/1来确定，1代表该值显示，0代表该值不显示

```
查询全部
db.user.find()

查询全部，但只显示name和games
db.user.find({},{name:1,games:1,_id:0})
```

### 比较操作符

中文含义|操作符|英文
--|--|--
小于< |$lt|less than
小于等于<= |$lte|less  than equal
大于> | $gt | greater than 
大于等于 >= | $gte | greater than equal
不等于!=|$ne|not equal
范围内in|$in|in
不在范围内|$nin|not in 
或or|$or|or
取反|$not|not
```
查询年龄大于24的人的name
db.user.find({age:{$gt:24}},{name:1})

查询名称不是张的name
db.user.find({name:{$nin:['zhang']}},{name:1})

查询名称是zhang或者bao的name
db.user.find({$or:[{name:'zhang'},{name:'bao'}]},{name:1})

查询名称中含有a字符的人的name
db.user.find({name:/a/},{name:1})

查询名称中不含有a字符的人的name
db.user.find({name:{$not:/a/}},{name:1})

查询同时喜欢coc和lol的人的name
db.user.find({games:{$all:['coc','lol']}},{name:1})

查询喜欢的游戏个数是4的人的name
db.user.find({games:{$size:4}},{name:1})

查询读了oracle书籍的人的name
db.user.find({'books.name':'oracle'},{name:1})

查询读了oracle和mysql书籍的人的name
db.user.find({'books.name':{$all:['oracle','mysql']}},{name:1})
/* 此处应该有更严谨的方式 ，以后补充 */
```
> **$size不能与比较操作符一起使用**

### 
