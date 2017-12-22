---
title: elasticsearch-修改文档
tags: java 
categories: elasticsearch
date: 2017-12-06
---

## Update API 更新

### 准备工作
<!-- more -->
①.先查询一个存在的文档
```java
@Test
public void query() throws IOException {
    GetRequest getRequest = new GetRequest("person","info","1");
    GetResponse getResponse = client.get(getRequest) ;
    if( getResponse.isExists() ) {
        System.out.println(getResponse.getSourceAsString());
    } else {
        System.out.println(" not found ");
    }
    
}
```

②.查询一个不存在的文档
```java
@Test
public void query() throws IOException {
    GetRequest getRequest = new GetRequest("person","info","17");
    GetResponse getResponse = client.get(getRequest) ;
    if( getResponse.isExists() ) {
        System.out.println(getResponse.getSourceAsString());
    } else {
        System.out.println(" not found ");
    }
}
```
### Update Request 实例化
```java
UpdateRequest update = new UpdateRequest(indexName,type,documentId);
```
更新API允许通过使用脚本或传递部分文档来更新现有文档。
> The Update API allows to update an existing document by using a script or by passing a partial document.

#### 使用脚本

> The script can be provided as an inline script:
```java
Map<String, Object> parameters = singletonMap("count", 4); 

Script inline = new Script(ScriptType.INLINE, "painless", "ctx._source.field += params.count", parameters);  
request.script(inline);  
```
> Or as a stored script: 
```java
Script stored =
        new Script(ScriptType.STORED, null, "increment-field", parameters);  
request.script(stored); 
```

#### 局部更新

> 局部更新需要提供部分文档内容，可以使用 [IndexApi](../../../03/elasticsearch/03-Index API/index.html) 中四种方法生成文档。

#### Upserts 

如果文档尚不存在，则可以使用upsert方法定义一些将作为新文档插入的内容：
> If the document does not already exist, it is possible to define some content that will be inserted as a new document using the upsert method.

代码说明
通过②方式确定id为17的文档找不到
```java
@Test
public void update ()  {
    UpdateRequest request = new UpdateRequest("person","info","17");
    Map<String,Object> map = new HashMap<String,Object>();
    map.put("name", "John");
    map.put("age", 26);
    map.put("msg", "very good!");
    request.doc(map);

    //如果没有找到文档，则新增
    //这里的map 同样适用于4中生成文档的方式
    update.upsert(map);

    UpdateResponse response;
    try {
        response = client.update(request);
    } catch (ElasticsearchException  e) {
        System.out.println(e.status());
    } catch (Exception e) {
        e.printStackTrace();
    }
}
```

#### 可选参数

##### 路由

```java
request.routing("routing");
```
##### 父级文档id

```java
request.parent("parent"); 
```
##### 版本号
```java
request.version(2); 
```
##### 超时时间

```java
request.timeout(TimeValue.timeValueMinutes(2)); 
request.timeout("2m"); 
```
> Timeout to wait for primary shard to become available as a TimeValue
> Timeout to wait for primary shard to become available as a String

##### 刷新策略

```java
request.setRefreshPolicy(WriteRequest.RefreshPolicy.WAIT_UNTIL); 
request.setRefreshPolicy("wait_for"); 
```
> Refresh policy as a WriteRequest.RefreshPolicy instance
> Refresh policy as a String

##### 冲突时更新动作进行几次
要更新的文档在更新过程中被另一个操作修改，则重试更新操作的次数
```java
request.retryOnConflict(3); 
```

##### 源搜索
默认为true
```java
request.fetchSource(true); 
```

##### 局部查询
同getapi中 *局部查询*
```java
String[] includes = new String[]{"updated", "r*"};
String[] excludes = Strings.EMPTY_ARRAY;
request.fetchSource(new FetchSourceContext(true, includes, excludes)); 

String[] includes = Strings.EMPTY_ARRAY;
String[] excludes = new String[]{"updated"};
request.fetchSource(new FetchSourceContext(true, includes, excludes)); 
```

##### 禁用无操作检测

无操作
该文档没有收到update操作的影响，也就是在该文档上没有任何操作执行
> the case where the document was not impacted by the update, ie no operation (noop) was executed on the document

```java
request.detectNoop(false); 
```

##### 脚本控制
无论文档是否存在，脚本都必须运行，即如果文档不存在，则脚本负责创建文档。
> Indicate that the script must run regardless of whether the document exists or not, ie the script takes care of creating the document if it does not already exist.
```java
request.scriptedUpsert(true); 
```
##### upsert控制
如果文档不存在，那么就根据给定的局部文档创建新文档。
```java
request.docAsUpsert(true);
```

##### ???

设置在继续更新操作之前必须激活的分片副本的数量。
> Sets the number of shard copies that must be active before proceeding with the update operation.

作为ActiveShardCount提供的分片副本数：可以是ActiveShardCount.ALL，ActiveShardCount.ONE或ActiveShardCount.DEFAULT（默认值）
> Number of shard copies provided as a ActiveShardCount: can be ActiveShardCount.ALL, ActiveShardCount.ONE or ActiveShardCount.DEFAULT (default)

```java
request.waitForActiveShards(2); 
request.waitForActiveShards(ActiveShardCount.ALL); 
```

---
### UpdateResponse

#### 执行方式
- 同步
```java
UpdateResponse updateResponse = client.update(request);
```
- 异步
```java
client.updateAsync(request, new ActionListener<UpdateResponse>() {
    @Override
    public void onResponse(UpdateResponse updateResponse) {
        //更新成功
    }

    @Override
    public void onFailure(Exception e) {
        //更新失败
    }
});
```

#### 响应参数
```java
String index = updateResponse.getIndex();
String type = updateResponse.getType();
String id = updateResponse.getId();
long version = updateResponse.getVersion();
if (updateResponse.getResult() == DocWriteResponse.Result.CREATED) {
    // 文档创建 (upsert)
} else if (updateResponse.getResult() == DocWriteResponse.Result.UPDATED) {
    // 文档更新
} else if (updateResponse.getResult() == DocWriteResponse.Result.DELETED) {
    // 文档删除
} else if (updateResponse.getResult() == DocWriteResponse.Result.NOOP) {
    //文档无操作
}
```

如果使用了 fetchSource 那么可以使用如下方法打印更新后的文档
```java
GetResult result = updateResponse.getGetResult(); 
if (result.isExists()) {
    String sourceAsString = result.sourceAsString(); 
    Map<String, Object> sourceAsMap = result.sourceAsMap(); 
    byte[] sourceAsBytes = result.source(); 
} else {
    //处理文档来源不存在于响应中的场景（默认情况下是这种情况）
}
```

分片失败情况
```java
ReplicationResponse.ShardInfo shardInfo = updateResponse.getShardInfo();
if (shardInfo.getTotal() != shardInfo.getSuccessful()) {
    //成功更新的分片数量不等于分片总数的情况
}
if (shardInfo.getFailed() > 0) {
    for (ReplicationResponse.ShardInfo.Failure failure : shardInfo.getFailures()) {
        String reason = failure.reason(); 
        //处理潜在的失败情况
    }
}
```

404找不到文档
```java
UpdateRequest request = new UpdateRequest("posts", "type", "does_not_exist").doc("field", "value");
try {
    UpdateResponse updateResponse = client.update(request);
} catch (ElasticsearchException e) {
    if (e.status() == RestStatus.NOT_FOUND) {
        //404
    }
}
```

文档版本冲突

```java
try {
    UpdateResponse updateResponse = client.update(request);
} catch(ElasticsearchException e) {
    if (e.status() == RestStatus.CONFLICT) {
        //版本冲突
    }
}
```
