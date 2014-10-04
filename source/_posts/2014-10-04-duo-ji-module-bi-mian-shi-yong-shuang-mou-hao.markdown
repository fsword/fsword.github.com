---
layout: post
title: "多级module避免使用双冒号"
date: 2014-10-04 11:48
comments: true
categories: ruby, double\_colon
---

在多层模块中定义类或者模块一般有两种做法：
```ruby
module A
  module B
    class C
    ...
    end
  end
end
```

和

```ruby
module A::B
  class C
  ...
  end
end
```

直观看，后者显然要简洁一些，所以我们常常使用这种写法，不过，其实后者更灵活。

这两种写法并不是等价的：

```ruby
module A
  module B
    Sth = "hello"
  end
end
module A
  module B
    module C
      class X
        def hello
          puts Sth
        end
      end
    end
  end
end


A::B::C::X.new.hello
```

执行上述代码：
```
$ ruby sample.rb 
hello
```

改为双冒号：
```ruby
module A
  module B
    Sth = "hello"
  end
end
module A::B::C
  class X
    def hello
      puts Sth
    end
  end
end


A::B::C::X.new.hello
```

再执行就会出错了
```
$ ruby sample.rb 
v.rb:9:in `hello': uninitialized constant A::B::C::X::Sth (NameError)
    from v.rb:15:in `<main>'
```

如果你感觉奇怪，说明对ruby的语法理解的还不充分。初学者常以为module和class这两个关键字是"`定义`一个类/模块"，但其实它们的意思是"`打开`一个类/模块"（正是这一特性让ruby具备了open class的能力），而对于`打开`这个概念，随之而来的自然是可以访问这个类/模块上下文的各种常量。

在前一种写法中，我们实际上是在`依次打开`模块A、B、C，因此X可以访问这些模块中的常量，而`module A::B::C`这种写法只是打开了` A::B::C `这个模块，于是X就不能访问 A::B 这个模块的常量了。

module中的常量，通常是一些模块级的共享对象和数据，因此，为了让我们的代码能够访问这些位置，写成层次module显然比使用双引号的代码更有灵活性。
