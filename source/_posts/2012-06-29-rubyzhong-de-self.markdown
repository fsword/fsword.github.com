---
layout: post
title: "ruby中的self"
date: 2012-06-29 09:38
comments: true
categories: ruby 基本概念
---
ruby-china.org 上有人问[self的含义](http://ruby-china.org/topics/4026)，发篇帖子解释一下  
ruby里面的class关键字和def关键字的作用其实是**改变上下文**，这个`self`就是被改变的`上下文`中最重要的一个，按照ruby语法，遇到这样的关键字，self的含义就会变化  
* 在`class内部`，self代表的是`当前这个类本身`  
```ruby
$ cat a.rb
class A
  puts self
end
$ ruby a.rb 
A
```
* 而通过`def`进入方法以后，（在方法内部写的）self指的是`这个方法的当前调用者`  
```ruby
$ cat a.rb 
class A
  def x
    p self
  end
end
A.new.x
$ ruby a.rb 
#<A:0x00000002705fb0>
```
如上所示，这次打印出来的self是类A的一个实例而不是类A本身  
  
这两个原则非常重要，现在我们看看方法前加self是什么意思  
```ruby
$ cat a.rb 
class A
  def self.x
    p self
  end
end
A.x
A.new.x
$ ruby a.rb 
A
a.rb:7:in `<main>': undefined method `x' for #<A:0x00000001a83f58> (NoMethodError)
```
显然，这个方法看起来像是我们常说的“类方法”而不是实例方法（这个例子中，实例方法x不存在，于是抛出了异常），我们通常这么理解——  
> 方法前的self在class内部，所以它表示类A，这样，x方法的调用者是**类A自身**（而不是它的实例），根据之前的原则，在def关键字内部，self表示的是这个方法的调用者，在这里就是类A自己  

最后补充一下——  
* ruby中的类本身也是对象，所以所谓“类方法”是个不太准确的描述，所有的方法都是属于某个对象的，这里的self.x其实是类A（**再次强调：是A自己而不是A的实例**）的“专有方法”，更进一步的理解要结合对eigenclass这个概念的理解
