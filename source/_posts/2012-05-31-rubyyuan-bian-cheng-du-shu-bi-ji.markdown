---
layout: post
title: "ruby元编程读书笔记"
date: 2012-05-31 21:14
comments: true
categories: 
---
本文记录在《ruby 元编程》这本书中学到的一些知识点。

# 对象模型

## class 关键字
这个关键字更象是一个作用域操作符而不是类型声明语句，它的核心任务是将代码带到类的上下文中，可以从这个角度理解 `open class`  
## 模块的实现
以下面的代码为例   
```ruby
module X;end

class   A
    include X
end

class  B < A; end
```
实际情况下，ruby 将生成一个匿名类来封装模块 X ，在最终的继承链上，这个匿名类将在包含 X 的类 A 之上，这样，类 B 的继承链就是  
```bash
    B < A < X(shadow) < Object < Kernel(shadow) < BasicObject
```
###### * 以上细节在 superclass 这个 api 上反映不出来，不过每个类可以调用自己的 ancestors 方法看到细节  
```ruby
     1.9.3p194 :001 > String.ancestors
      => [String, Comparable, Object, Kernel, BasicObject]
```
###### * 多个 include 的情况  
```
module X;end
module Y;end

class   A
    include X
    include Y
end

class  B < A; end

B.ancestors
 => [B, A, Y, X, Object, Kernel, BasicObject]
```
