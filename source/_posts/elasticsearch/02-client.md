---
title: elasticsearch-客户端
tags: java 
categories: elasticsearch
date: 2017-12-02
---

#### 创建简单的高级别客户端

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
//关闭客户端
public void clientClose() throws Exception {
    if ( !Objects.isNull(client) ) {
        client.close();
    }
}
```

***此处 client 会在以后的文章中使用***