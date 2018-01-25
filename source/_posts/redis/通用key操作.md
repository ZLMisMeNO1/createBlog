---
title: redis-通用key操作
tags: redis
categories: nosql
date: 2018-01-02
---


1. 查询所有key（根据正则表达式获取key）
> keys pattern
> ?代表一个任意字符；*代表任意字符
```
    keys ag[e|g] 查age或agg
    keys * : 查所有key
``` 
2. 随机获取一个key： randomkey
3. 查看值类型： type key
4. 查看key是否存在：exists key 
    - 存在返回1
    - 不存在返回0
<!-- more-->
5. 删除key：del key
    - 成功返回1
    - 失败返回0
6. 重命名
    - 普通重命名：rename key newname
    - 安全重命名：renamenx key newname
        - nx = not exists 
        - 如果新名字不存在时重命名成功返回1，新名字已经存在时失败返回0
7. 移动key： move key dbIndex
8. 查询key有效期:永久有效返回-1，key不存在返回-2
    - ttl key：返回单位为秒
    - pttl key：返回单位为毫秒
9. 设置key的有效期
    - expire key 整数值  （单位秒）
    - pexpire key 毫秒值 （单位毫秒）
10. 设置key永久有效： persist key