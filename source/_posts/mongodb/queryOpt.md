---
title: mongodb-查询操作
tags: mongodb
categories: nosql
date: 2018-01-25
---

### 数据准备
```
1. use test
2. db.user.insert({name:'zhang',age:25,games:['lol','coc','cf','pubg'],books:[{type:'js',name:'jQuery'},{type:'db',name:'oracle'},{type:'db',name:'mysql'}]})
3. db.user.insert({name:'gu',age:22,games:['phone','lol'],books:[{type:'js',name:'jQuery'},{type:'server',name:'java'},{type:'db',name:'mysql'}]})
4. db.user.insert({name:'bao',age:12,games:['coc','lol'],books:[{type:'db',name:'oracle'},{type:'server',name:'java'},{type:'db',name:'mysql'}]})

```

### 限制查询数据条数 limit

```
查询一条用户数据
db.user.find().limit(1) √
db.user.findOne() √
```

<!--more-->
### 限制查询数据跨度 skip
```
查询第二条数据（跨过一条）
db.user.find().limit(1).skip(1)
```
### 数据排序 sort 
> 1:正序（小---大） ；-1:反序（大---小）

**注意**
mongodb的key可以存不同类型的数据，排序就也有优先级
最小值
null
数字
字符串
对象/文档
数组
二进制
对象ID
布尔
日期
时间戳
正则
最大值

```
按age从大到小查询所有数据
db.user.find().sort({age:-1})
```
### 分页

> db.collectionName.find().limit( rows ).skip( (page-1) * rows )
```
skip有性能问题，没有特殊情况我们也可以换个思路来实现分页。
在文档中添加date属性，每次查询操作时，前后台传值时要把上次的最后一个文档的日期保存传过来。
db.user.find({date:{$gt:日期}}).limit(rows)
```

### 利用游标遍历查询数据
```
var persons = db.user.find();
while (persons.hasNext() ) {
    var obj = persons.next();
    print(obj)
} 
```
游标销毁条件
1. 客户端发来消息叫他销毁
2. 游标迭代完毕
3. 默认游标超过10分钟没用也会被清除