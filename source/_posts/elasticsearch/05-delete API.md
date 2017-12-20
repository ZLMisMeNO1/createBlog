---
title: elasticsearch-删除文档
tags: java 
categories: elasticsearch
date: 2017-12-05
---

## Delete API 删除

### Delete Request实例化

```java
DeleteRequest request = new DeleteRequest(indexName,type,documentId);
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

##### 版本及类型

```java
request.version(2); 
request.versionType(VersionType.EXTERNAL); 
```

--- 

### DeleteResponse 

- 同步方式

```java
DeleteResponse deleteResponse = client.delete(request);
```

- 异步方式
```java
client.deleteAsync(request, new ActionListener<DeleteResponse>() {
    @Override
    public void onResponse(DeleteResponse deleteResponse) {
        
    }

    @Override
    public void onFailure(Exception e) {
        
    }
});
```

#### DeleteResponse方法
```java
String index = deleteResponse.getIndex();
String type = deleteResponse.getType();
String id = deleteResponse.getId();
long version = deleteResponse.getVersion();

ReplicationResponse.ShardInfo shardInfo = deleteResponse.getShardInfo();
if (shardInfo.getTotal() != shardInfo.getSuccessful()) {
    //处理 成功的碎片数量 少于 总碎片数 的情况
}
if (shardInfo.getFailed() > 0) {
    for (ReplicationResponse.ShardInfo.Failure failure : shardInfo.getFailures()) {
        String reason = failure.reason(); 
        //处理失败
    }
}
```

##### 错误处理
###### 404

```java
DeleteRequest request = new DeleteRequest("posts", "doc", "does_not_exist");
DeleteResponse deleteResponse = client.delete(request);
if (deleteResponse.getResult() == DocWriteResponse.Result.NOT_FOUND) {
    //没有找到文档错误 404
}
```

###### 版本冲突

```java
try {
    DeleteRequest request = new DeleteRequest("posts", "doc", "1").version(2);
    DeleteResponse deleteResponse = client.delete(request);
} catch (ElasticsearchException exception) {
    if (exception.status() == RestStatus.CONFLICT) {
        //版本冲突错误
    }
}
```