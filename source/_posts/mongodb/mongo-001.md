---
title: mongodb-常用配置项、与关系型数据库的对比、命名规则
tags: nosql
categories: mongodb
date: 2018-01-01
---


默认端口：27017
28017是web服务端口

---

### 常用配置项

--dbpath:指定数据库的目录
--port:指定端口
--fork：用守护线程的方式启动mongodb
--logpath：指定日志的输出路径
--auth：用安全认证的方式启动数据库
<!--more-->
---
### mongodb和关系型数据库的对比

对比项|MongoDB|关系型数据库
--|--|--
表|集合List|二维表table
表的一行数据|文档document|记录
表字段|键key|字段field
字段值|值value|值value
主外键|无|pk、fk
灵活度、扩展性|极高|差

区别总结：
- 关系型数据库的表中记录必须保证拥有每一个field；mongodb的每一个document的键key可以不一样
- 关系型数据库查询使用sql；mongodb查询用find()[基于bson的特殊查询]

---
### 数据库命名规则

- 不能是空字符串
- 不得含有''(空格),\,.,/,$和\0(空字符串)
- 应全部小写
- 最多64个字符
- 数据库名不能与现有系统保留库同名，admin,config...

#### 案例
db-test虽然是合法的，但是不能通过db.[collectionName]得到，要改为db.getCollection([collectionName]),因为'-'会被认为是减法操作。

---
### 在使用mongodb VUE可视化工具时，应使用mmapv/存储引擎启动，