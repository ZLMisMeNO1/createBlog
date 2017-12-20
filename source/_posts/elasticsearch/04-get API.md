---
title: elasticsearch-获取文档
tags: java 
categories: elasticsearch
date: 2017-12-04
---

## Get API 获取

### Get Request实例化

```java
GetRequest request = new GetRequest(indexName,type,documentId);
```
e.g.
```java
public void get() throws IOException {
    GetRequest getRequest = new GetRequest("person","info","1");
    GetResponse getResponse = client.get(getRequest);
    System.out.println(getResponse.getSourceAsString());

    //打印结果
    //{"id":"1","name":"name_1","age":1,"updateTime":1513654831475,"birth":1513654216715,"hight":0.6375151,"weight":0.4946818126143968,"isBoy":false}
}
```

#### 可选参数

##### 设置版本号
```java
getRequest.version(long version);
```

##### 局部查询
```java
getRequest.fetchSourceContext(FetchSourceContext context);
```

###### 举例说明
```java
String[] includes = new String[]{"name","hight","updateTime"};
String[] excludes = Strings.EMPTY_ARRAY;
FetchSourceContext fetchSourceContext = new FetchSourceContext(true, includes, excludes);
getRequest.fetchSourceContext(fetchSourceContext); 
GetResponse getResponse = client.get(getRequest);
System.out.println(" get: " + getResponse.getSourceAsString());
//只获取检索到的数据中 name height updateTime 字段的值
//打印结果  get: {"name":"name_3","hight":0.84175926,"updateTime":1513654831475}
```

```java
String[] includes = Strings.EMPTY_ARRAY;
String[] excludes = new String[]{"name","hight","updateTime"};
FetchSourceContext fetchSourceContext = new FetchSourceContext(true, includes, excludes);
getRequest.fetchSourceContext(fetchSourceContext);
GetResponse getResponse = client.get(getRequest);
System.out.println(" get: " + getResponse.getSourceAsString());
//与上个正好相反，获取检索到的数据中除了 name height updateTime字段其他字段的值
//打印结果  get: {"birth":1513654216715,"weight":0.3806846418323202,"id":"3","age":3,"isBoy":false}
```

##### 路由

```java
getRequest.routing("routing");
```
##### 父级文档id

```java
getRequest.parent("parent"); 
```
##### ?
> Configure retrieval for specific stored fields (requires fields to be stored separately in the mappings)
> Retrieve the message stored field (requires the field to be stored separately in the mappings)

```java
getRequest.storedFields("message"); 
GetResponse getResponse = client.get(getRequest);
String message = getResponse.getField("message").getValue(); 
```

##### Preference 
```java
getRequest.preference("preference");  
```

##### realtime flag 

```java
getRequest.realtime(false);
//默认为true  
```

##### refresh
```java
getRequest.refresh(true);
//默认为false  
```

##### versionType
```java
getRequest.versionType(VersionType.EXTERNAL);
//默认为false  
```
---
### Get Response

- 同步方式

```java
GetResponse getResponse = client.get(getRequest);
```

- 异步方式
```java
client.getAsync(getRequest, new ActionListener<GetResponse>(){
    @Override
    public void onResponse(GetResponse getResponse) {
        //正常响应
        System.out.println(" getAsync: " +getResponse.getSourceAsString());
        
    }

    @Override
    public void onFailure(Exception e) {
        //出错
        e.printStackTrace();
    }
});
```
#### GetResponse方法

```java
//index名称
String index = getResponse.getIndex();
//类型
String type = getResponse.getType();
//文档id
String id = getResponse.getId();

//文档是否存在
if (getResponse.isExists()) {
    //文档版本
    long version = getResponse.getVersion();
    //文档以字符串的形式返回
    String sourceAsString = getResponse.getSourceAsString();  
     //文档以Map的形式返回      
    Map<String, Object> sourceAsMap = getResponse.getSourceAsMap(); 
     //文档以字节的形式返回
    byte[] sourceAsBytes = getResponse.getSourceAsBytes();          
} else {
    //处理找不到文档的情况。 请注意，尽管返回的响应具有404状态码，但返回的是有效的GetResponse，而不是抛出的异常。 这样的响应不包含任何源文件，其isExists方法返回false。
}
```

##### 错误处理
###### 404
```java
GetRequest request = new GetRequest("does_not_exist", "doc", "1");
try {
    GetResponse getResponse = client.get(request);
} catch (ElasticsearchException e) {
    if (e.status() == RestStatus.NOT_FOUND) {
        //当针对不存在的索引执行获取请求时，响应具有404状态码，将引发ElasticsearchException
    }
}
```
###### 版本冲突
```java
try {
    GetRequest request = new GetRequest("posts", "doc", "1").version(2);
    GetResponse getResponse = client.get(request);
} catch (ElasticsearchException exception) {
    if (exception.status() == RestStatus.CONFLICT) {
        //如果请求了特定的文档版本，并且现有文档具有不同的版本号，则会引发版本冲突：
    }
}
```