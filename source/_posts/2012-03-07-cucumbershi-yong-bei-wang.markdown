---
layout: post
title: "cucumber使用备忘"
date: 2012-03-07 00:59
comments: true
categories: 
---
cucumber 使用的时候默认的环境是 test ，此时配置信息为 config/environments/test.rb  
当然我们也可以使用独立的 cucumber ，修改 config/database.yml 如下： 
```
production:
  <<: *defaults
  database: app_development


cucumber:
  <<: *defaults
  database: app_cucumber
```
此时需要复制 test.rb 为 cucumber.rb  
$ cp config/environments/test.rb config/environments/cucumber.rb  
这时需要注意，如果你使用transaction进行数据清理，那么要设置 `config.cache_classes = true `，否则会收到错误信息
```
WARNING: You have set Rails' config.cache_classes to false (most likely in config/environments/cucumber.rb).  This setting is known to cause problems with database transactions. Set config.cache_classes to true if you want to use transactions.  For more information see https://rspec.lighthouseapp.com/projects/16211/tickets/165.
```
不过，如果这样，rails s启动的服务器将不会热更新代码，这点需要注意  
我自己的做法是，支持热更新，但是不使用 transaction 方式支持数据清理  
