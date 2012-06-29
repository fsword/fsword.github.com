---
layout: post
title: "关于Class/Module的讨论"
date: 2012-06-02 11:46
comments: true
categories: 
---
Ruby China 上有人提了一个问题：

```
class Test < Module; end
test = Test.new

test到底是个神马玩意？
```
这个问题的回答首先要看看Ruby的对象/类层次设计，如下图（ <= 表示泛化，<- 表示继承）：

    user_instance <= UserClass < Class < Module < Object < BasicObject
                                 |  |      |         |
                                 |   <=====          |
                                  <==================

显然，Class是Module的子类，所以一般由用户创建的class都是Class的实例，也就是Module的实例
```ruby
1.9.3p194 :001 > class A; end
 => nil 
1.9.3p194 :002 > A.is_a? Class
 => true 
1.9.3p194 :003 > A.is_a? Module
 => true 
```
但如果一个类继承了 Class或者Module，那么它理论上应该是Class/Module的子类，其实例才是Class/Module的实例
```ruby
1.9.3p194 :001 > class A; end
 => nil 
1.9.3p194 :002 > class X < Module; end
 => nil 
1.9.3p194 :003 > A.new.is_a? Module
 => false 
1.9.3p194 :004 > X.new.is_a? Module
 => true 
```
这里有个不太好理解的地方，X本身是继承了Module，所以是Module的子类，但是它又是用class关键字创建的，所以它还是Class的实例，换句话说，X和X.new都是Module的实例
```ruby
1.9.3p194 :005 > X.is_a? Module
 => true
```
当然，合理的推论是，如果用户定义一个类是继承自Class，也会出现上述的情况，不过这一点从语法上已经被排除了——ruby禁止直接继承Class，可是并不反对继承Class的父类Module，这就是导致这个问题复杂的原因
