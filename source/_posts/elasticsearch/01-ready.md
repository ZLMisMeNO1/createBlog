---
title: elasticsearch-前期准备（window + eclipse）
tags: java 
categories: elasticsearch
date: 2017-12-01
---

#### 本机状态
- java -version 1.8.0_151
- elasticsearch 6.0.0
- elasticsearch-rest-high-level-client  6.0.1
#### 前期准备

<!-- more -->
- java
    - jdk1.8及以上
    - 配置环境变量
- elasticsearch
    - 6.0.0
> java安装及环境变量自行Google/baidu

---
##### elasticsearch安装流程
[elasticsearch下载地址](https://www.elastic.co/cn/downloads/elasticsearch)
- 下载elasticsearch zip压缩包
- 解压
- cd bin目录
- shift + 鼠标右键  --> '在此处打开命令窗口（W）' 
- 输入elasticsearch 
- 服务端完毕

---
##### eclipse

- jdk1.8以上
- maven 依赖
    ```maven
    <dependency>
		    <groupId>org.elasticsearch.client</groupId>
		    <artifactId>elasticsearch-rest-high-level-client</artifactId>
		    <version>6.0.1</version>
    </dependency>
    ```


--- 
#### 本机测试速度
> 本机测试33万数据可以达到毫秒级的模糊查询





