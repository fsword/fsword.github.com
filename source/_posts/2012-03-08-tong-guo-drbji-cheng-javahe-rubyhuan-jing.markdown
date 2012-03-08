---
layout: post
title: "通过drb集成java和ruby环境"
date: 2012-03-08 23:51
comments: true
categories: 
---
ruby在很多方面都很不错，但是java也有它的优势，至少我们有很多基于java的遗留系统。  
结合这两者主要有几种思路：  
* 使用消息系统链接java应用和ruby应用，这是我们通常整合异构系统的思路  
* 基于java的分布式设施进行系统整合，这要将ruby放在jvm上工作，我们可以用jruby on rails  
* 基于ruby的 drb 技术进行系统整合，我们同样需要借助 jruby 让java系统看起来象 ruby  
前两个不用举例，最后一个给一个简单的示例
```
# server.rb
require 'drb'

DRb.start_service('druby://localhost:9000', self)
```
以上的代码如果在 rails console 上执行，就可以使用如下代码进行远程调用了：  
```
# client
require 'drb'

DRb.start_service
this = DRbObject.new(nil, 'druby://localhost:9000')

this.class_eval 'Rails.application.config.root'
```

Have fun!
