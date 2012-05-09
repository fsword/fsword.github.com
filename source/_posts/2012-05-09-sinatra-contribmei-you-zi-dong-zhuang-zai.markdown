---
layout: post
title: "sinatra-contrib没有自动装载"
date: 2012-05-09 18:53
comments: true
categories: ruby bundle
---
最近写代码是基于sinatra的，遇到了一点小问题  
在Gemfile中添加了sinatra和sinatra-contrib，但是实际使用中，sinatra-contrib没有被装入
```ruby
# Gemfile
gem 'sinatra'
gem 'sinatra-contrib'
```
解决的办法是在启动脚本里面手工加上一行require
```
require "bundler"
Bundler.require(:default)
require 'sinatra/contrib'
```
但是这个方式有点土，其实如果换用新版的bundle就可以自动处理了
```
$ gem update bundle
Updating installed gems
Updating bundler
Fetching: bundler-1.1.3.gem (100%)
    Successfully installed bundler-1.1.3
    Updating rubygems-bundler
    Fetching: rubygems-bundler-0.9.2.gem (100%)
    Building native extensions.  This could take a while...
    Successfully installed rubygems-bundler-0.9.2
    Gems updated: bundler, rubygems-bundler
```
需要说明的是，bundler1.1目前在网站上写的还是 coming soon，不过新功能还是值得尝试的  
顺便附上 [bundler 的 changlog](https://github.com/carlhuda/bundler/blob/master/CHANGELOG.md)
