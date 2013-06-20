---
layout: post
title: "单元测试不是持续集成的基础2-项目中的例子"
date: 2013-06-07 19:27
comments: true
categories: 
---
前一篇博客写完以后觉得有些空，这篇里面想说说现实中的例子。

淘宝是java集散地，我们以java项目为例，下面是一个项目中的测试代码——

```java
    @Test             
    public void addCreative() {
        CreativeDO creative = new CreativeDO();
        ...此处省略11行set语句...
        long creativeId = this.creativeDAO.addCreative(creative);
        Assert.assertTrue(creativeId > 0);
    }
```

这个test方法只是用了最基本的验证方式，显然这是用于确保基本功能——能够正确的添加记录，充其量只是用来验证和数据库相关的配置文件是否正确

我们继续看上层代码——
```java
public class CreativeServiceImpl implements CreativeService{
    ...
    public ResultDTO<Long> addCreative(CreativeDTO creativeDTO) {
        ...
        creativeDAO.addCreative(do);
        ...
    }
}
```
这个类有自己的测试保护，不过这次我们就不要看那些代码了，先用vim看看outline吧——
```   
|-   class          
||     CreativeServiceTest  
|-   field
||     creativeService [CreativeServiceTest]
||     creativeDAO [CreativeServiceTest]
||     ...
|-   method 
||     before [CreativeServiceTest]
||     ...此处省略10个用于测试addCreative的用例...
||     addCreative [CreativeServiceTest]
```
显然，服务层方法有非常充分的测试保护，通过服务层调用，creativeDTO也间接得到了验证，其功能覆盖要远大于之前单独针对DAO的测试用例。

那么，我们为什么要写那个DTO的测试呢？不但费力（11行set，每一行都需要精心填入合理的数据），还用处不大。

出现这个现象一般有两个原因——  
* 实践中，工程师有时是先写DAO再写上层模块，这样，写完DAO以后service还不存在，要验证是正确性就必须写DAO的test case。
* service的运行一般是依赖某些服务容器的，为它编写的test case相当于服务器外部的一个客户端，而这需要将service部署起来。

前一个原因还好办，很多java团队都意识到DAO和相邻的软件层之间存在某些重复性，因而可以用类似代码模板的方式一次性生成DAO、Service。这时，service和DAO是一起出现的，这样，我们就可以直接关心service了。

而后一个原因则是困难所在，因为在没有持续集成意识并建立持续集成机制的团队中，“部署应用”这件事是所有工作中相对靠后的一步，通常是开发时间过半以后才有第一次部署，而这时团队成员们已经重复的写了很多测试用例了......
