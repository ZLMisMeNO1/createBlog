---
title: elasticsearch-批量处理
tags: java 
categories: elasticsearch
date: 2017-12-07
---

### BULK API 批量处理

** *批量API仅支持以JSON或SMILE编码的文档。 以任何其他格式提供文档将导致错误。* **
> The Bulk API supports only documents encoded in JSON or SMILE. Providing documents in any other format will result in an error.


#### BulkRequest 
BulkRequest可以用于使用单一请求多个操作，
或多个更新或删除操作。

添加索引
```java
BulkRequest request = new BulkRequest(); 
request.add(new IndexRequest("posts", "doc", "1")  
        .source(XContentType.JSON,"field", "foo"));
request.add(new IndexRequest("posts", "doc", "2")  
        .source(XContentType.JSON,"field", "bar"));
request.add(new IndexRequest("posts", "doc", "3")  
        .source(XContentType.JSON,"field", "baz"));
```

多个操作
```java
BulkRequest request = new BulkRequest();
request.add(new DeleteRequest("posts", "doc", "3")); 
request.add(new UpdateRequest("posts", "doc", "2") 
        .doc(XContentType.JSON,"other", "test"));
request.add(new IndexRequest("posts", "doc", "4")  
        .source(XContentType.JSON,"field", "baz"));
```

##### 可选参数

###### 超时
```java
request.timeout(TimeValue.timeValueMinutes(2)); 
request.timeout("2m"); 
```
###### 刷新策略
```java
request.setRefreshPolicy(WriteRequest.RefreshPolicy.WAIT_UNTIL); 
request.setRefreshPolicy("wait_for"); 
```

###### 分片
设置在进行索引/更新/删除操作之前必须激活的分片副本的数量。
```java
request.waitForActiveShards(2); 
request.waitForActiveShards(ActiveShardCount.ALL); 
```
---
### BulkResponse 批量响应
- 同步
```java
BulkResponse bulkResponse = client.bulk(request);
```
- 异步
```java
client.bulkAsync(request, new ActionListener<BulkResponse>() {
    @Override
    public void onResponse(BulkResponse bulkResponse) {
        //执行成功完成时调用。 响应作为参数提供，并包含每个已执行操作的单独结果列表。
        // 请注意，一个或多个操作失败，不会影响其他操作。
    }

    @Override
    public void onFailure(Exception e) {
        //整个BulkRequest失败时调用。 在这种情况下，引发的异常是作为参数提供的，没有执行任何操作。
    }
});
```

响应是批量的，可以用iterator查看每个具体响应
```java
for (BulkItemResponse bulkItemResponse : bulkResponse) { 
    DocWriteResponse itemResponse = bulkItemResponse.getResponse(); 

    if (bulkItemResponse.getOpType() == DocWriteRequest.OpType.INDEX
            || bulkItemResponse.getOpType() == DocWriteRequest.OpType.CREATE) { 
        IndexResponse indexResponse = (IndexResponse) itemResponse;
        //创建索引

    } else if (bulkItemResponse.getOpType() == DocWriteRequest.OpType.UPDATE) { 
        UpdateResponse updateResponse = (UpdateResponse) itemResponse;
        //更新文档

    } else if (bulkItemResponse.getOpType() == DocWriteRequest.OpType.DELETE) { 
        DeleteResponse deleteResponse = (DeleteResponse) itemResponse;
        //删除文档
    }
}
```

查看错误信息
```java
//如果有一个出现错误，则返回true
if (bulkResponse.hasFailures()) { 

}
```

也可以进行单独的错误检查
```java
for (BulkItemResponse bulkItemResponse : bulkResponse) {
    if (bulkItemResponse.isFailed()) { 
        BulkItemResponse.Failure failure = bulkItemResponse.getFailure(); 

    }
}
```
---
### Bulk Processor

BulkProcessor通过提供一个实用程序类来简化Bulk API的使用，允许在添加到处理器时索引/更新/删除操作被透明地执行。

> The BulkProcessor simplifies the usage of the Bulk API by providing a utility class that allows index/update/delete operations to be transparently executed as they are added to the processor.

为了执行请求，BulkProcessor需要3个组件：
> In order to execute the requests, the BulkProcessor requires 3 components:

RestHighLevelClient 客户端
处理批量请求和响应
>   This client is used to execute the BulkRequest and to retrieve the BulkResponse

BulkProcessor.Listener 监听器
在批量请求执行或失败的前后监听
>   This listener is called before and after every BulkRequest execution or when a BulkRequest failed

ThreadPool 线程池
BulkRequest执行使用来自该池的线程完成，允许BulkProcessor以非阻塞方式工作，并在执行批量请求时接受新的索引/更新/删除请求
>   The BulkRequest executions are done using threads from this pool, allowing the BulkProcessor to work in a non-blocking manner and to accept new index/update/delete requests while bulk requests are executing.

代码说明
```java
ThreadPool threadPool = new ThreadPool(Settings.EMPTY) ;

BulkProcessor.Listener listener = new BulkProcessor.Listener() {
    
    @Override
    public void beforeBulk(long executionId, BulkRequest request) {
        //执行前
        
        //此方法可以知道在BulkRequest中要执行的操作的数量
        int numberOfActions = request.numberOfActions(); 
        System.out.println(numberOfActions);
    }
    
    @Override
    public void afterBulk(long executionId, BulkRequest request,
            Throwable failure) {
        //执行失败
    }
    
    @Override
    public void afterBulk(long executionId, BulkRequest request,
            BulkResponse response) {
        //执行成功
    }
};

BulkProcessor.Builder builder = new BulkProcessor.Builder(client::bulkAsync, listener, threadPool);

//根据当前添加的操作数设置何时刷新新的批量请求（默认为1000，使用-1来禁用它）
builder.setBulkActions(500); 
//根据当前添加的操作的大小设置何时刷新新的批量请求（默认为5Mb，使用-1来禁用它）
builder.setBulkSize(new ByteSizeValue(1L, ByteSizeUnit.MB)); 
//设置允许执行的并发请求数（默认为1，使用0只允许执行一个请求）
builder.setConcurrentRequests(0); 
//设置时间间隔，如果超过时间间隔，将会清理未执行的请求
builder.setFlushInterval(TimeValue.timeValueSeconds(10L)); 
//设置重试策略，最初等待1秒钟，然后重试3次
builder.setBackoffPolicy(BackoffPolicy.constantBackoff(TimeValue.timeValueSeconds(1L), 3)); 

BulkProcessor bulkProcessor = builder.build();


IndexRequest one = new IndexRequest("person", "info", "20").
        source(XContentType.JSON, "title", "In which order are my Elasticsearch queries executed?");
IndexRequest two = new IndexRequest("person", "info", "21")
        .source(XContentType.JSON, "title", "Current status and upcoming changes in Elasticsearch");
IndexRequest three = new IndexRequest("person", "info", "22")
        .source(XContentType.JSON, "title", "The Future of Federated Search in Elasticsearch");

bulkProcessor.add(one);
bulkProcessor.add(two);
bulkProcessor.add(three);

//awaitClose方法可以 在所有请求都完成 或 指定的时间内过去了  后关闭bulkProcessor
//如果所有批量请求都完成，则该方法返回true，如果在所有批量请求完成之前经过了等待时间，则返回false
boolean terminated = bulkProcessor.awaitClose(30L, TimeUnit.SECONDS);
System.out.println(terminated);
//也可以使用clse()方法立即关闭processor
//这两种方法在关闭处理器之前刷新添加到处理器的请求，并禁止添加新的请求。
//bulkProcessor.close();
```