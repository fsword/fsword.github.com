---
layout: post
title: "持续集成中的缺环-3"
date: 2012-12-13 13:45
comments: true
categories: 
---
终于要讲到 DSL 了，其实这个话题我更加感兴趣一些，这也是ruby的强项 :-)

我们首先应该从用户角度出发，看看用户写出来的 dsl 应该是什么样子，例如像一个配置文件：
```ruby
# -*- encoding : UTF-8 -*-

# 定义web server类型，可选值: apache, nginx, tengine
# web_server :apache
web_server :apache

# 定义应用服务器类型，可选值: tomcat, jboss
# app_server :tomcat # option: jboss

# 申请数据库资源
# from: 有时需要从已有的数据库中复制数据，from参数用于指明来源的数据库名称
db :from => 'sample'

# 设定源代码获取方式，目前仅支持git和svn两种
source :svn => 'http://svn.yourserver.com/branches/some_branch'

# 设定安装包的来源，目前仅支持 rpm 包安装
# 注意：如果设定了安装包，adsci将跳过build环境，此时source设定不会生效
pkg   :rpm => 'http://yum.yourserver.com/your_package.rpm'

# 设定 mock 服务，指定的服务将用 mock 支持
mock :service => ['myservice.core.1.0.0']

# 设定所依赖的其它 profile ，需要在指定 profile 的服务/应用启动后再启动
after :portal  do
  # 在指定的 profile （这个例子中是 portal 应用）中为当前节点添加 url rewrite 规则
  rewrite :for => :me
end

# 设定依赖的其它 profile ，不考虑启动顺序
after :portal  do
  route :for => :me
end
```
真正的 DSL 应该比这个更强大一些，不过从这样开始应该不错。  

> 硬广告：这个文件比较简单，唯一需要说明的是tengine，这是淘宝基于nginx扩展的一个开源项目，好事者可以看[这里](https://github.com/taobao/tengine)。

用户通过这种方式说明自己的应用应该是怎样部署和工作的，其它事情交给工具。

实现DSL最常见的做法就是把DSL语句变成一个函数，然后在定义函数的上下文中eval用户的脚本，比如一个最简单的语法`web_server :apache`，
可以这么写——
```ruby
module Dsl
  def web_server name
    puts "设定web server为#{name}"
  end
end
```
执行结果——
```irb
1.9.3p327 :008 > include Dsl
 => Object 
1.9.3p327 :009 > eval("web_server :nginx")
设定web server为nginx
 => nil 
```
这就是最简单的配置项。  
但是这样做有什么意义呢？我们可以这样修改一下 Dsl 的代码——
```ruby
# -*- encoding : UTF-8 -*-
# 文件名 dsl.rb
module Dsl
  def web_server name
    if name == :apache
      `sudo /etc/init.d/httpd start`
    elsif name == :nginx
      `sudo /etc/init.d/nginx start`
    else
      puts '抱歉，目前不支持这种 web server'
      return
    end
    puts "启动 #{name}"
  end
end
```
同样也把eval的内容存为文件
```ruby
# -*- encoding : UTF-8 -*-
# 文件名 myapp
web_server :nginx
```
最后编写一个装载脚本
```ruby
# -*- encoding : UTF-8 -*-
#!/usr/bin/env ruby
# 文件名 run.rb
require './dsl'

include Dsl
eval File.read('myapp')
``` 
执行一下：
```
$ ruby run.rb 
[sudo] password for john: 
启动 nginx
```
这样就完成了一个最简单的 dsl 样例，在这个样例中，我们通过一个关键词 `web_server` 驱动了逻辑
