---
layout: post
title: "自定义ruby异常时需要注意的一个问题"
date: 2014-08-15 12:12
comments: true
categories: 
---

有时我们会有这样的场景，对于依次调用的函数 A 、B、 C，存在这样的职责：

```
A [处理指定异常类]
    -----> B[转换异常类] 
                -----> C[抛出原始异常]
```

有人会写出这样的代码：
```ruby
class WrappedError < StandardError; end
def first
  second
rescue WrappedError => e
  puts "#{e.message}: \n#{e.backtrace.join "\n"}"
end

def second
  third
rescue StandardError => e
  # do some things
  raise WrappedError.new e
end

def third
  fail 'I am wrong'
end

first
```

这个写法有两个错误：

* java的异常类通常会在构造器方法里传入root cause对象，但ruby不需要，它可以直接从 $! 上得到，所以 `WrappedError.new e`其实多余。
* ruby的异常经过包装后就不会在backtrace中输出之前的root cause信息，不过异常对象有cause属性可以用来访问，所以在first函数里应该这么写——

```ruby
puts "#{e.message}: \n#{(e.cause||e).backtrace.join "\n"}"
```

have fun!
