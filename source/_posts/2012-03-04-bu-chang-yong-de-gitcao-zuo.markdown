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

## git push -f origin master
说明：强制push本地代码，例如在本地进行 amend 模式的commit的时候，实际上是重写了原来的commit，如果直接push会出错，此时需要强制push，让remote端与本地一致  
使用举例：
```
$ git commit --amend -m 'this is a new comment'
To /home/john/proj_repo
 ! [rejected]        master -> master (non-fast-forward)
error: failed to push some refs to '/home/john/proj_repo'
To prevent you from losing history, non-fast-forward updates were rejected
Merge the remote changes (e.g. 'git pull') before pushing again.  See the
'Note about fast-forwards' section of 'git push --help' for details.
~/personal/temp2(master) $ git push -f origin master 
$ git push -f origin master 
Counting objects: 3, done.
Writing objects: 100% (3/3), 260 bytes, done.
Total 3 (delta 0), reused 0 (delta 0)
Unpacking objects: 100% (3/3), done.
To /home/john/proj_repo
 + a11c377...93ef44d master -> master (forced update)
```
要说明的是，如果要做这个试验，需要确保remote允许push，如果你像我一样remote和work都是本地目录，那么remote那里要设置一下 .git/config 这个文件，增加这么一段
```
[receive]
    denyCurrentBranch = ignore
```
另外，对于pull的一方，操作也要稍微调整一下：  
```
git fetch origin
git reset --hard origin/master
```

have fun!
