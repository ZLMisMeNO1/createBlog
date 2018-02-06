---
title: mongodb-快照
tags: mongodb
categories: nosql
date: 2018-01-25
---

**本文中，大部分都在3.2后被注释掉了**
[官方文档说明](https://docs.mongodb.com/manual/reference/operator/meta/query/)

### 快照的意义

```
5个文档，查询不做快照

|①|②|③|④|⑤|

当循环到第三个时，在③中加入了值，③的内存变大
我们以为的是

|①|②|③   ③   ③|④|⑤|
实际上是
|①|②|⑤|④|③   ③   ③|

mongo会将数据重新变化，为③扩容
```
<!-- more-->
### 查询快照
不做快照容易造成错误值
快照后就会针对不变的集合进行游标运动
```
db.user.find({$query:{name:'zhang'},$snapshot:true})
```
### 高级查询选项

$query 查询
$orderby 排序
$maxscan: 最多扫描的文档
$min:doc 查询开始
$max:doc 查询结束
$hint:使用的索引
$explain:boolean 统计
$snapshot:boolean 快照