---
title: elasticsearch-客户端
tags: java 
categories: elasticsearch
date: 2017-12-02
---



#### Java High Level Rest Client 高级rest客户端

我们计划在Elasticsearch 7.0 中弃用TransportClient，在Elasticsearch 8.0时完全移除。相反，推荐使用the Java High Level REST Client，它执行HTTP请求而不是序列化java请求。
> We plan on deprecating the TransportClient in Elasticsearch 7.0 and removing it completely in 8.0. Instead, you should be using the Java High Level REST Client, which executes HTTP requests rather than serialized Java requests. 

java高级rest客户端目前支持更常用的api，但仍然还有更多需要添加的东西。
> The Java High Level REST Client currently has support for the more commonly used APIs, but there are a lot more that still need to be added. 

任何缺少的api都可以通过使用具有json请求和响应主体的低级java rest客户端来实现。
> Any missing APIs can always be implemented today by using the low level Java REST Client with JSON request and response bodies.

[关于javaHighLevelRestClien](https://www.elastic.co/guide/en/elasticsearch/client/java-rest/current/java-rest-high.html)

> The Java High Level REST Client works on top of the Java Low Level REST client. Its main goal is to expose API specific methods, that accept request objects as an argument and return response objects, so that request marshalling and response un-marshalling is handled by the client itself.

Java高级别REST客户端在Java低级别REST客户端上工作。 其主要目标是公开API接口的具体方法，接受请求对象作为参数并返回响应对象，以便请求编组和响应解组由客户端自己处理。


> Each API can be called synchronously or asynchronously. The synchronous methods return a response object, while the asynchronous methods, whose names end with the async suffix, require a listener argument that is notified (on the thread pool managed by the low level client) once a response or an error is received.

每个API都可以同步或异步调用。 同步方法返回一个响应对象，而异步方法的名称以异步后缀（async）结尾，需要一个监听器参数，一旦收到响应或错误，会通知（在低级客户端管理的线程池中）。

> The Java High Level REST Client depends on the Elasticsearch core project. It accepts the same request arguments as the TransportClient and returns the same response objects.

Java高级REST客户端依赖于Elasticsearch核心项目。 它接受与TransportClient相同的请求参数，并返回相同的响应对象。

#### 创建简单的高级别客户端

> A RestHighLevelClient instance needs a REST low-level client builder to be built as follows:

```java
private RestHighLevelClient client = null;
//创建客户端
public void getClient() {
    client = new RestHighLevelClient(
        RestClient.builder(
            new HttpHost("localhost",9200,"http");
        );
    );
}
```
高级客户端将在内部创建用于基于提供的构建器执行请求的低级客户端，并管理其生命周期。
> The high-level client will internally create the low-level client used to perform requests based on the provided builder, and manage its lifecycle.


当不再需要高级客户端实例时，需要关闭高级客户端实例，以便使用的所有资源以及底层http客户端实例及其线程都得到正确释放。 这可以通过close方法来完成，这将关闭RestClient的内部实例。
> The high-level client instance needs to be closed when no longer needed so that all the resources used by it get properly released, as well as the underlying http client instance and its threads. This can be done through the close method, which will close the internal RestClient instance.



```java
//关闭客户端
public void clientClose() throws Exception {
    if ( !Objects.isNull(client) ) {
        client.close();
    }
}
```

***此处 client 会在以后的文章中使用***


