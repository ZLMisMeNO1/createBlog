---
title: mongodb-2D索引
tags: mongodb
categories: nosql
date: 2018-01-25
---

mongodb提供强大的空间索引，可以查询出一定范围内的地理坐标，数据，map，json

### 数据准备
```
db.map.insert({gis:{x:0,y:0}})
db.map.insert({gis:{x:0,y:200}})
db.map.insert({gis:{x:50,y:50}})
db.map.insert({gis:{x:60,y:55}})
db.map.insert({gis:{x:55,y:80}})
db.map.insert({gis:{x:65,y:80}})
db.map.insert({gis:{x:180,y:150}})
db.map.insert({gis:{x:70,y:180}})
db.map.insert({gis:{x:75,y:180}})
db.map.insert({gis:{x:65,y:185}})
db.map.insert({gis:{x:185,y:185}})
db.map.insert({gis:{x:200,y:200}})
db.map.insert({gis:{x:200,y:0}})
```
<!-- more-->
### 添加2D索引

```
db.map.ensureIndex({gis:'2d'},{min:-1,max:201})
如果不指定min和max，则默认会床架一个[-180,180]之间的2D索引
```

#### 查询距离点(70,180)最近的3个点

```
db.map.find({gis:{$near:[70,180]}}).limit(3)
```

#### 查询出以点(50,50)和(190,190)为对角线的正方形中所有的点

```
db.map.find({gis:{$within:{$box:[[50,50],[190,190]]}}})
```

#### 查询出以点(56,80)为圆心，半径为50的圆面积下的点

```
db.map.find({gis:{$within:{$center:[[56,80],50]}}})
```

### 总结
1. $near + 坐标，在坐标附近
2. $within + 区域 ，在区域里
3. $box + [[点1],[点2]] ， 以点1和点2为对角线的正方形区域
4. $center + [[圆点],半径]， 圆形区域
