---
layout: post
title: "不常用的git命令"
date: 2012-03-04 18:18
comments: true
categories: 
---
## git remote prune   
说明：remote上的一个分支被其他人删除后，需要更新本地的分支列表   
使用举例：   
```
$ git branch -a
* master
  remotes/origin/master
  remotes/origin/other
$ git remote prune origin
Pruning origin
URL: git@github.com:fsword/my_project.git
 * [pruned] origin/other
$ git branch -a
* master
  remotes/origin/master
```
