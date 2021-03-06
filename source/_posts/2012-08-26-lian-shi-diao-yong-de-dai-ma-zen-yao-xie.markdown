---
layout: post
title: "链式调用的代码怎么写"
date: 2012-08-26 13:55
comments: true
categories: 链式调用 代码技巧
---
使用ruby，一个很有价值的地方就是发明了各种DSL，比如这样：  
```ruby
tags.delete_if{|x| x.nil?}.map{|v| v.sub /^!/,''}.delete_if{|x| x.empty?}
```
这种风格通常被称为链式调用，优点是代码比较连贯，去掉了多余的局部变量和赋值语句，符合人类的思维习惯。  

但是也有隐含的问题——代码可读性有时候会受影响，例如这里的例子中，对数组的变换包括三个步骤，如果看的不仔细，就可能会遗漏。  

按照我自己的经验，一般会改成这样：  
```ruby
tags.
     delete_if(&:nil?).
     map{|v| v.sub /^!/,''}.
     delete_if(&:empty?)
```
这样，每行都是一个独立单一的逻辑部分，可读性就有了保障。
问题并没有结束，链式调用的另外一个常见问题是对于故障的处理，使用者有时会搞不清楚到底在哪里出了错。  

由于这种api一般会使用延迟计算的方式（延迟计算是个大的话题，请google），之前的环节仅仅是收集计算所需要的逻辑，真正出问题总是在最后，而那时的错误可以输出完整的计算条件，所以大部分情况下故障处理还不是很复杂。   

但是如果有的api设计的不好或者出现了意料以外的问题，我们怎么办呢？  

处理故障最常用的工具是日志，而链式调用不太好做的也就是日志，好在ruby的标准库提供了一个很好用的api——tap（很多人一开始看到这个函数，发现什么都不做，觉得很奇怪），我们看下面的例子  
```ruby
tags                       .tap{|x|logger.info "origin size: #{x.size}"    }
    .delete_if(&:nil?)     .tap{|x|logger.info "del nil    : #{x.size}"    }
    .map{|v| v.sub /^!/,''}.tap{|x|logger.info "sub        : #{x.inspect}" }
    .delete_if(&:empty?)   .tap{|x|logger.info "del empty  : #{x.size}"    }
```
这样的代码，既方便了连贯的表达业务逻辑，又能在必要的时候输出需要的日志，基本上就比较靠谱了，顺便在说一句，我专门调整了代码的对齐并不是简单的为了好看，如果有一天你确定这段代码不再需要日志输出，那么使用很多编辑器都支持的列编辑功能就可以一次性删除掉日志了  
