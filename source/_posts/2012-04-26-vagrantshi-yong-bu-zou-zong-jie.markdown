---
layout: post
title: "vagrant使用步骤总结"
date: 2012-04-26 01:04
comments: true
categories: vagrant virtualization puppet rvm
---
使用vagrant把AppOSS的项目环境打了一个包，这个东西确实很适合统一开发环境，总结一下过程吧。

**基于base.box或者某个os的iso文件建立基础系统环境，相关命令为**
```
vagrant add base <base box url>
cd <your rails project>
vagrnat init base
vagrant up
```
**调整系统环境，相关命令为**
```
vagrant ssh
curl -L get.rvm.io | bash -s stable
```
**将调整过的环境打包为新的base文件，以备将来使用，相关命令为**
```
vagrant package --output base-ubuntu-rvm.box
```
**修改Vagrantfile，添加puppet支持，相关内容为**
```
# Vagrantfile
config.vm.provision :puppet do |puppet|
  puppet.manifests_path = "manifests"
  puppet.manifest_file  = "ubuntu.pp"
end
```
**将项目依赖的软件包（例如：libxml2-dev，libmysqlclient-dev之类），通过puppet安装好，相关命令为**
```
vagrant reload
```
**重新打包为box文件，这就是可以重复使用的项目环境了**
```
vagrant package --output apposs-center.box
```
说明：  
1. 对于团队协作而言，如果环境有了变化，重复5、6两步即可   
2. 将rvm放在自己定制的base中是考虑到其它项目也会用到rvm，原则上，项目专用的环境通过puppet进行统一，各项目都有的环境使用同一个base box来解决  
3. ruby项目通过bundle管理的gem包，可以采用下面的命令进行安装，这样可以减少内外系统的重复文件
```
bundle install --path ./vendor/bundle
```
这个做法是来自saberma同学，原文在此： http://saberma.me/linux/2011/03/03/vagrant-virtual-develop-enviroment.html
