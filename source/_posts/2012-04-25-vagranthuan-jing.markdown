---
layout: post
title: "vagrant环境"
date: 2012-04-25 11:34
comments: true
categories: vagrant virtualization
---
vagrant是一个基于Virtual Box的ruby工具库，它可以很简单的管理你的开发和运行环境
官方网站：http://vagrantup.com/

今天准备了一个干净的运行环境，步骤如下：  

** 修改locale **
```  
vi /etc/default/locale #将 LANG 改为 zh_CN.utf8
sudo locale-gen zh_CN.utf8
```

** 安装必要的本地库 **
```
sudo apt-get install mysql-server libmysqlclient-dev git
sudo apt-get install libxslt-dev libxml2-dev
```

** 用rvm安装ruby **
```
curl -L get.rvm.io | bash -s stable
source .bashrc
rvm pkg install zlib
rvm install ruby-1.9.3
```

**进入 /vagrant 目录，准备rails环境**
```
rake db:create db:migrate
rails s
```
后续结合puppet来进行环境管理
