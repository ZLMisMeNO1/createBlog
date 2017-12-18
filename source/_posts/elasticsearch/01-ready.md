---
title: elasticsearch-前期准备（window + eclipse）
tags: java 
categories: elasticsearch
date: 2017-12-01
---



#### 前期准备

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
#### 通俗理解es

> 通俗的理解es（elasticsearch):es是一个仓库，可以存放任何东西，我们能向仓库寄存东西，也能从仓库取东西。
> 寄存东西的时候要告诉他，物品是要存放在哪个小屋子里（index)，属于什么类型（type）的,物品的回执单可以自己写，也可以通过> 仓库提供。
> 取东西的时候要告诉他，是哪个屋子里的什么类型的东西，或者更具体一点，哪个屋子中类型是啥的回执单上的字是什么的。

> 可以按照范围获取东西，也可以统计，类似数据库。

--- 
#### 本机测试速度
> 本机测试33万数据可以达到毫秒级的模糊查询





