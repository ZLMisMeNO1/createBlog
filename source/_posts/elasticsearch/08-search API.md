---
title: elasticsearch-查询接口
tags: java 
categories: elasticsearch
date: 2017-12-08
---

### Search API查询接口

#### SearchRequest

对于查询来说，SearchRequest与其他的Request差不多，最重要的是创建查询。

```java
//可以选择性的添加索引库 indexName
SearchRequest request = new SearchRequest([indexName]);
//类型
request.types(...typeName);
//路由信息
request.routing("routing"); 
//设置IndicesOptions控制不可用索引如何解析以及如何扩展通配符表达式
request.indicesOptions(IndicesOptions.lenientExpandOpen()); 
//使用偏好参数例如 执行搜索以选择本地碎片。 默认是在整个分片上随机化。
searchRequest.preference("_local"); 
```

<!-- more -->

##### 创建查询源 SearchSourceBuilder

通过SearchSourceBuilder来创建一个查询构建器。
```java
SearchSourceBuilder builder = new SearchSourceBuilder(); 
//查询结果 从第一个开始显示5条数据
builder.from(0).size(5); 
//查询超时时间
builder.timeout(new TimeValue(60, TimeUnit.SECONDS)); 
//排序 如果使用了排序的话，那么score 会为 NaN
//SearchSourceBuilder允许添加一个或多个SortBuilder实例。 
// 有四个特殊的实现（Field-，Score-，GeoDistance-和ScriptSortBuilder）。
builder.sort(new ScoreSortBuilder().order(SortOrder.DESC)); // 默认_score
builder.sort(new FieldSortBuilder("_uid").order(SortOrder.ASC)); 

//过滤 fetchSource
//同其他fetchSource
String[] includeFields = new String[] {"title", "user", "innerObject.*"};
String[] excludeFields = new String[] {"_type"};
builder.fetchSource(includeFields, excludeFields);

```

创建查询
```java
builder.query(QueryBuilder); 
```
query方法接收一个QueryBuilder，就是一个具体的查询语句。

eg.创建一个TermQuery
```java
builder.query(QueryBuilders.termQuery("name", "John"));
```
eg.创建一个范围查询
```java
RangeQueryBuilder rangeQuery = QueryBuilders.rangeQuery("age").from(0).to(30);
builder.query(rangeQuery);
```
更多查询 [参见文档](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/6.0/java-rest-high-query-builders.html)


最后将构建器加入到request中
```java
request.source(builder);
```

高亮搜索
```java
SearchSourceBuilder builder = new SearchSourceBuilder();
MatchPhrasePrefixQueryBuilder matchPhrasePreficQuery = new MatchPhrasePrefixQueryBuilder("msg", "v");
builder.query(matchPhrasePreficQuery);
HighlightBuilder hight = new HighlightBuilder();

//设置高亮字段
HighlightBuilder.Field msg = new HighlightBuilder.Field("msg");
hight.field(msg);
//查询构造器加入高亮
builder.highlighter(hight);
//部分抓取
builder.fetchSource(new String[]{"name","age","msg"}, null);

request.source(builder);

//查询后的响应
SearchResponse response = client.search(request);

SearchHits hits = response.getHits();
float maxScore = hits.getMaxScore();
System.out.println("最大分数:" + maxScore);
long searchSize = hits.totalHits;
System.out.println("搜索到 " + searchSize + " 条记录");
for (SearchHit hit : hits ) {
    System.out.println("原始记录:" + hit.getSourceAsString() + ",权重：" + hit.getScore());
    //处理高亮字段
    Map<String, HighlightField> highlightFields = hit.getHighlightFields();
    Text[] fragments = highlight.fragments();  
    String fragmentString = fragments[0].string();
    System.out.println(highlightFields);
}
```



