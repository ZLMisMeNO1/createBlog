---
title: mongodb-文档查询
tags: mongodb
categories: nosql
date: 2018-01-24
---

### 数据准备
```
1. use test
2. db.user.insert({name:'zhang',age:25,games:['lol','coc','cf','pubg'],books:[{type:'js',name:'jQuery'},{type:'db',name:'oracle'},{type:'db',name:'mysql'}]})
3. db.user.insert({name:'gu',age:22,games:['phone','lol'],books:[{type:'js',name:'jQuery'},{type:'server',name:'java'},{type:'db',name:'mysql'}]})
4. db.user.insert({name:'bao',age:12,games:['coc','lol'],books:[{type:'db',name:'oracle'},{type:'server',name:'java'},{type:'db',name:'mysql'}]})
```
<!-- more-->
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

### $size

如果要查询游戏数为3的学生，则先要在文档中添加size属性，然后每次更新游戏时管理size，最后再使用该字段。
也就是：要解决$size不能与比较查询符一起使用的弊端，需要建立一个key存储这个size就行。

### $slice 操作符返回文档中指定数组的内部值(从1开始，包头包尾)
> $slice为-1时显示最后一个

```
查询name为zhang的用户，游戏只显示最后一个，书籍也只显示最后一个
db.user.find({name:'zhang'},{games:{$slice:-1},books:{$slice:-1}})

查询name为zhang的用户，游戏显示第一个和第二个，书籍只显示最后一个
db.user.find({name:'zhang'},{games:{$slice:[1,2]},books:{$slice:-1}})
```
### $elemMatch 数组中每一项都要匹配
```
数据准备

db.student.insert({name:'xiaogang',schools:[{school:'A',score:'B'},{school:'B',score:'B'}]})
db.student.insert({name:'xiaohong',schools:[{school:'A',score:'A'},{school:'B',score:'B'}]})
db.student.insert({name:'xiaoli',schools:[{school:'A',score:'A'},{school:'B',score:'A'}]})
db.student.insert({name:'xiaozhao',schools:[{school:'A',score:'B'},{school:'B',score:'A'}]})

查询在学校A获得A成绩的学生
错误示范
db.student.find({'schools.school':'A','schools.score':'A'})  ×
正确示范
db.student.find({schools:{$elemMatch:{school:'A',score:'A'}}}) √

```

### $where 复杂查询
复杂查询由$where万能查询器来实现，但要尽量避免使用，有性能代价。

```
查询在学校A获得A成绩的学生
db.student.find({'$where':function(){
    var schools = this.schools;
    for ( var i=0;i<schools.length;i++) {
        if ( schools[i].school == 'A' && schools[i].score == 'A') {
            return true;
        }
    }
}})
```