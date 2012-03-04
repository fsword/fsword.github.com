---
layout: post
title: "fixture使用时需要注意的问题"
date: 2012-03-04 17:47
comments: true
categories: 
---
单测时不太顺利，主要是fixture使用不熟悉，记录两个问题    

bug 1：经过定位发现是fixture数据始终不能灌入导致的    
```
machine1:
  id: 1
  app_id: 1
machine1:
  id: 2
  app_id: 1
```
期望有两条数据，实际只有一条，原因是在rspec灌数据时装载yml，而这里的数据是个hash，结果key相同（都是“machine1”）的entry被覆盖了，后一条被装入。    
解决办法是：`检查并修改重名的fixture条目，确保不冲突`  

bug 2：经过定位发现是fixture数据导入时，某个条目出错    
```
something:
  id: 2
  app_id: 1
  name: package
  expression: 1,2
```
这里的 expression 值期望为字符串 `1,2` ，然而最后总是变成 `12`，后来才发现是格式问题，对于,这种特殊字符，不能省略字符串的双引号，改为 "1,2"    
解决办法是：`检查并修改fixture条目中的格式特别的字符串，确保使用双引号包含`  
