---
layout: post
title: "require使用例子"
date: 2013-06-18 17:08
comments: true
categories: ruby基础
---

常常有人搞不清楚ruby脚本怎么装载，其实ruby有一个$LOAD\_PATH全局变量，相当于java的classpath，require就是从这里的目录中进行查找的

```
$ ls
boot.rb
$ irb
irb(main):001:0> require 'boot'
LoadError: cannot load such file -- boot
    from /home/john/.rvm/rubies/ruby-1.9.3-p392/lib/ruby/site_ruby/1.9.1/rubygems/custom_require.rb:36:in `require'
    from /home/john/.rvm/rubies/ruby-1.9.3-p392/lib/ruby/site_ruby/1.9.1/rubygems/custom_require.rb:36:in `require'
    from (irb):1
    from /home/john/.rvm/rubies/ruby-1.9.3-p392/bin/irb:13:in `<main>'
irb(main):002:0> puts $LOAD_PATH
/home/john/.rvm/rubies/ruby-1.9.3-p392/lib/ruby/site_ruby/1.9.1
/home/john/.rvm/rubies/ruby-1.9.3-p392/lib/ruby/site_ruby/1.9.1/x86_64-linux
/home/john/.rvm/rubies/ruby-1.9.3-p392/lib/ruby/site_ruby
/home/john/.rvm/rubies/ruby-1.9.3-p392/lib/ruby/vendor_ruby/1.9.1
/home/john/.rvm/rubies/ruby-1.9.3-p392/lib/ruby/vendor_ruby/1.9.1/x86_64-linux
/home/john/.rvm/rubies/ruby-1.9.3-p392/lib/ruby/vendor_ruby
/home/john/.rvm/rubies/ruby-1.9.3-p392/lib/ruby/1.9.1
/home/john/.rvm/rubies/ruby-1.9.3-p392/lib/ruby/1.9.1/x86_64-linux
=> nil
irb(main):003:0> $LOAD_PATH << '.'
=> ["/home/john/.rvm/rubies/ruby-1.9.3-p392/lib/ruby/site_ruby/1.9.1", "/home/john/.rvm/rubies/ruby-1.9.3-p392/lib/ruby/site_ruby/1.9.1/x86_64-linux", "/home/john/.rvm/rubies/ruby-1.9.3-p392/lib/ruby/site_ruby", "/home/john/.rvm/rubies/ruby-1.9.3-p392/lib/ruby/vendor_ruby/1.9.1", "/home/john/.rvm/rubies/ruby-1.9.3-p392/lib/ruby/vendor_ruby/1.9.1/x86_64-linux", "/home/john/.rvm/rubies/ruby-1.9.3-p392/lib/ruby/vendor_ruby", "/home/john/.rvm/rubies/ruby-1.9.3-p392/lib/ruby/1.9.1", "/home/john/.rvm/rubies/ruby-1.9.3-p392/lib/ruby/1.9.1/x86_64-linux", "."]
irb(main):004:0> require 'boot'
=> true
```
