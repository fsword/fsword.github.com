---
layout: post
title: "一个bug的修改"
date: 2013-08-27 16:28
comments: true
categories: 代码片段
---

下面是一段工作中编写的代码，为便于理解修改如下——

```ruby
class Order
  attr_accessor :type, :sub_orders

  # 统计订单及其包含子订单的数量，按照订单类型归类
  def count_items
    result = sub_orders.reduce({}) do |result, order|
      order.count_items.each do |key, value|
        result[key] = (result[key]||0) + value
      end
    end
    result[type] = (result[type]||0) + 1
    result
  end
end
```

乍一看没问题，但是计算出来的数字始终不对，细看才发现问题，修改一下：

```ruby
  def count_items
    result = sub_orders.reduce({}) do |result, order|
      order.count_items.each do |key, value|
        result[key] = (result[key]||0) + value
      end
      result #添加这一行
    end
    result[type] = (result[type]||0) + 1
    result
  end
```

分析原因，是对reduce的使用不够细致：each和map容易和以前写java代码时的思维方式一致，所以不容易犯错；而reduce的返回值用于进一步迭代，这种做法以前用的较少，潜意识里还是将result和s看作是一个服务于循环的变量。

想明白以后再带着新的角度看这个代码，于是发现还可以简化，reduce操作的最大特点就是将初始值和结果纳入到计算框架中，这样可以减少很多重复劳动，最后改成这样

```ruby
  def count_items
    sub_orders.reduce({ type => 1 }) do |result, order|
      order.count_items.reduce(result) do |s, (key, value)|
        s.update key => (s[key]||0) + value
      end
    end
  end
```

其实就两点：1. block参数的模式匹配；2. update的返回值就是当前对象

试验代码在 [这里](https://gist.github.com/fsword/6353526) ，Be fun!
