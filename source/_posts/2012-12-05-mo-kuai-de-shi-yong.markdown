---
layout: post
title: "模块的使用"
date: 2012-12-05 22:23
comments: true
categories: 
---
我们常常使用模块，一般是这样
```ruby
module X
  def hello; 'hello'; end
  def world; 'world'; end
end
```

使用起来很简单
```ruby
1.9.3p327 :006 >   include X
=> Object 
1.9.3p327 :007 > hello
=> "hello" 
```

不过，有时候我不想先要include一下才能使用，这是会遇到错误
```ruby
1.9.3p327 :006 > X.hello
NoMethodError: undefined method `hello' for X:Module
    from (irb):6
    from /home/john/.rvm/rubies/ruby-1.9.3-p327-falcon/bin/irb:16:in `<main>'
```

这是因为使用 def 定义的方法都是 module 的实例方法，当然，就象可以在class里定义类方法一样，module也可以这么做
```ruby
module X
  def self.hello; 'hello'; end
  def self.world; 'world'; end
end

# in irb
1.9.3p327 :007 > X.hello
=> "hello" 
```

对于方法很多的 module ，我们可以少写几个`self.`，只要借助 extend 机制
```ruby
module X
  extend self
  def hello; 'hello'; end
  def world; 'world'; end
end

# in irb
1.9.3p327 :007 >   X.hello
 => "hello" 
1.9.3p327 :008 > include X
 => Object 
1.9.3p327 :009 > hello
 => "hello" 
```

这个 extend 的作用是把当前这个module extend出去，使实例方法同时承担类方法的职责，那么，如果我们不希望这些方法被include呢，今天看 I18n的代码，学了一招

```ruby
module X
  extend Module.new{
    def hello; 'hello'; end
    def world; 'world'; end
  }
end
```
由于extend的不是当前module，而是一个匿名module，这块“领地”其它代码接触不到，所以其内部的方法不能被include，达到了我们的要求
```ruby
1.9.3p327 :008 > X.hello
 => "hello" 
1.9.3p327 :009 > include X
 => Object 
1.9.3p327 :010 > hello
NameError: undefined local variable or method `hello' for main:Object
    from (irb):10
    from /home/john/.rvm/rubies/ruby-1.9.3-p327-falcon/bin/irb:16:in `<main>'
```
灵活运用module，可以很好的提高代码的复用性，同时保护好必要的封装，have fun !
