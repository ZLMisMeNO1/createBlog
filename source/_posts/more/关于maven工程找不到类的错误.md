---
title: eclipse关于maven工程找不到类的错误
tags: java
categories: 杂货
date: 2018-01-01
---

情况是这样的，在公司电脑里向GitHub提交了代码，maven工程。
回到家后下载下来，却提示找不到类 -_-!!
查看了一下提示的类确实存在，为什么找不到呢？

<!--more-->

解决方法：
项目-右键-properties-Deployment Assembly-Add-Java Build Path Entries-Maven-Finish
