---
title: mongodb-索引
tags: mongodb
categories: nosql
date: 2018-01-25
---

### 数据准备
```
for (var i=0;i<200000;i++) {
    db.books.insert({number:i,name:i+'book'})
}
```
### 建立简单索引

db.collectionName.ensureIndex({key:1/-1})
1:正序索引  -1:反序索引

```
为number建立正序索引
db.books.ensureIndex({number:1})
```
<!-- more-->
索引使用需要注意的地方
1. 索引的创建在提高查询性能的同时会影响插入的性能。对于经常查询少插入的文档可以考虑用索引
2. 使用索引注意索引的先后顺序
3. 每个键建立索引不一定就能提高性能，索引不是万能的。
4. 在做排序工作的时候，如果是超大数据量，也可以以考虑加上索引，用来提高排序的性能。

###  索引的名称

创建索引的同时，可以指定索引的名称。

```
db.books.ensureIndex({name:1},{name:'bookname'})
```

### 唯一索引

可以用来解决文档中不能插入重复的值，建立唯一索引

```
db.books.ensureIndex({name:1},{unique:true})
```

### 替出重复值

建立唯一索引之前已经有重复值如何处理

```
db.books.ensureIndex({name:1},{unique:true,dropDups:true}) //dropDups 是否删除重复值
```

### 在查询时强制使用索引
```
db.books.find().hint({name:1})
```
*指定索引必须是已经创建了的索引，否则报错*

### 查询时查看使用了哪个索引和查询数据的详细信息

```
db.books.findOne().explain()
```

### 查看数据库已经建立的索引

```
db.system.indexes.find()
db.system.namespace.find()
```

### 创建索引过程中会暂时锁表，为了不影响查询，可以让索引的创建过程在后台执行

```
db.books.ensureIndex({name:1},{background:true})
```

### 删除索引

#### 批量删除和精确删除
 
```
db.runCommand({dropIndexes:'books',index:'name_1'})
db.runCommand({dropIndexes:'books',index:'*'})
```