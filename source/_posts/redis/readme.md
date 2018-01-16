---
title: redis-安装
tags: nosql
categories: redis
date: 2018-01-01
---

# redis和memcached相比的独特之处
1. redis可用来做存储，memchched用来做缓存。
2. memcached只有字符串类型，redis可以存储字符串、链表、哈希结构、集合、有序集合

---
# 安装步骤
1. cd 目标目录
2. wget 压缩包地址
3. tar zxvf 文件名
4. make （32位：make 32bit）
5. 安装到指定文件夹：make PREFIX=/usr/local/redis install
6. 复制一份配置文件： cp redis.conf /usr/local/redis
7. 启动redis：bin/redis-server redis.conf
8. 开启客户端：bin/redis-cli
9. redis后台运行：在redis.conf文件中设置
    daemonize:yes
10. 查看redis是否运行：ps aux|grep redis
<!--more-->
> redis默认开启了16个数据库，可以在conf文件中database:16看到，
> 切换数据库：在客户端中，select index;默认使用0数据库。

---

# redis工具
1. redis-bechmark:性能测试工具
2. redis-check-aof:检查aof的工具
3. redis-check-dump:检查rdb的工具

---
<!--end-->