---
title: index api
tags: elasticsearch
categories: java 
date: 2017-12-03
---


[官方文档--Index API](https://www.elastic.co/guide/en/elasticsearch/client/java-api/6.0/java-docs-index.html)

### 生成JSON文档

生成方式

1.手动生成字符串形式
```java
public String jsonSource() {
	String jsonSource = "{" +
	        "\"country\":\"China VS US\"," +
	        "\"startTime\":\""+new Date()+"\"," +
	        "\"message\":\"China is ok !\"" +
	        "}";
	System.out.println(jsonSource);
	return jsonSource;
}
```
2.使用自己的bean
```java
public class TestBean {
    private String id;
    private String name;
    private Integer age;
        get/set ...
}
	
import com.fasterxml.jackson.databind.ObjectMapper;
	
private byte[] byteSource() throws Exception {
	TestBean testBean = new TestBean();
	testBean.setAge(11);
	testBean.setId("abcdefg");
	testBean.setName("i7baoz");
	ObjectMapper mapper = new ObjectMapper();
	return mapper.writeValueAsBytes(testBean);
}
```
***
> 在使用前两种方法是要注意：

> indexRequest.source()时要指定类型

> eg: indexRequest.source(byteSource(),XContentType.JSON);


> 否则会报错误：

> java.lang.IllegalArgumentException: The number of object passed must be even but was [1]

---
3.使用Map形式
```java
public Map<String,Object> mapSource() {
	Map<String,Object> mapSource = new HashMap<String,Object>();
	
	mapSource.put("country","China VS US");
	mapSource.put("startTime", new Date());
	mapSource.put("message", "China is ok !");
	return mapSource;
}
```
4.使用XContentBuilder 
```java
private XContentBuilder builderSource() throws Exception {
	XContentBuilder xcbSource = XContentFactory.jsonBuilder();
	xcbSource
		.startObject()
			.field("country","China VS US")
			.field("startTime",new Date())
			.field("message","China is ok !")
		.endObject();
	return xcbSource;
}
```
> 如果想看生成的原始json可以使用string()方法

> String json = xcbSource.string();


### 索引文档

> 理解为将文档保存

> 对比讲就是讲数据保存至数据库 -_-

```java
//分别提供 索引， 类型，文档id
//文档id不提供的话系统会自动生成
IndexRequest indexRequest = new IndexRequest(index,type,id);
//设置数据来源，以上4种方式
indexRequest.source(source [,XContentType.JSON]);
//设置操作类型
//默认为update
indexRequest.opType(OpType.CREATE/UPDATE/INDEX/DELETE)
//设置路由
indexRequest.routing([String])
//设置上级文档的id
indexRequest.parent("");
//设置刷新策略
indexRequest.setRefreshPolicy(RefreshPolicy);

//索引（保存）有两种方式
//一个是同步索引（保存）
//一个是异步索引（保存）

//同步索引
IndexResponse response = client.index(indexRequest);

//异步索引
client.indexAsync(indexRequst,new ActionListener<IndexResponse>(){
    @override
    public void onResponse(IndexResponse response) {
        //索引成功
    }
    
    @override
    public void onFailure(Exception e) {
        //索引失败 抛出异常信息
    }
});
    
//索引响应IndexResponse包含的信息

String indexName = response.getIndex();
String typeName = response.getType();
String documentId = response.getId();
long documentVersion = response.getVersion();
//操作状态  CREATED / UPDATED / DELETED / NOT_FOUND / NOOP
RestStatus status = response.status();
```
