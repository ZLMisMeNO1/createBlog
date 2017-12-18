---
title: elasticsearch-文档
tags: java 
categories: elasticsearch
date: 2017-11-30
---
***
#### 官方网站
[elastic](https://www.elastic.co/)

[官方文档](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-index_.html)

[官方中文文档](https://www.elastic.co/guide/cn/elasticsearch/guide/current/index.html)

[官方客户端api](https://www.elastic.co/guide/en/elasticsearch/client/index.html)
***

#### 相关中文简介以及简单操作
[阮一峰-全文搜索引擎 Elasticsearch 入门教程](http://www.ruanyifeng.com/blog/2017/08/elasticsearch.html)

##### 个人理解elasticsearch术语

关系型数据库 | elasticsearch 
----|----|
database | index
table | type
rows | documents

关于type字段
> Indices created in Elasticsearch 6.0.0 or later may only contain a single mapping type. Indices created in 5.x with multiple mapping types will continue to function as before in Elasticsearch 6.x. Mapping types will be completely removed in Elasticsearch 7.0.0.
 
> 将会在7.0.0. 后的版本移除

