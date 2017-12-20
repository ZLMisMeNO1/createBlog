---
title: 监听input textarea改变事件
tags: 前端
categories: jQuery
date: 2017-11-03
---

#### 监听input\textarea改变事件
```html
    <input type="text" id="txtInput">
    <textarea id="txtArea" >
```
#### 引入jQuery库

#### js

```javascript
    $("#txtInput").bind('input propertychange',function(){
        console.log($(this).val())
    });
    $('#txtArea').bind('input propertychange',function(){
        console.log($(this).val())
    });
```

#### 主要格式
> $(selecter).bind('input propertychange',callback);