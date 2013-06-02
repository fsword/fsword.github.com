---
layout: post
title: "单元测试不是持续集成的基础"
date: 2013-05-25 22:25
comments: true
categories: 
---

很多人关注甚至想尝试持续集成，然而也有一些人担心团队缺乏基础——“我们连单元测试都做不好，做持续集成不太合适吧”。如果不会走就学跑，那确实容易摔跤。不过，单元测试和持续集成并不是走和跑的关系。

为避免误解，首先明确一下名词(虽然没有照抄书本，但是应该不会差太远吧)：

* 单元测试：以验证某个代码单元的正确性为目标进行的自动化测试活动，“代码单元”通常是函数、方法或者是类，测试过程中，目标单元对外部的编译或者功能依赖由stub或者mock技术进行隔离。
* 持续集成：一种敏捷实践，重点是尽早进行系统的集成测试，“持续”一般被理解为不断的对研发变更进行整体验证，“集成”通常包括对分支的集成（因此一般推荐单分支开发）和在一定条件下对不同子系统或者模块进行的集成。

可以看出，单元测试针对的目标是局部而非整体，而持续集成面对的是整体。按照“饭要一口一口吃”的老话，似乎应该先做单元测试。

然而单元测试并不是免费的，任何自动化测试都是基于测试目标的功能而实现的，此时测试目标的稳定性就是影响自动化测试价值的一个重要因素，而作为粒度较小的类、方法和函数，业务上的变化对它的影响可能是天翻地覆的。

举个例子：
```java
@Controller
public class UserProfileAdminController {
    ...
    @RequestMapping(method = { RequestMethod.POST, RequestMethod.GET }, value = "/admin/profiledata")
    public ModelAndView getProfileData(HttpServletRequest request) {
        ...
        mv.addObject("memCacheValue", gUserProfileManager.getProfileValue(uKey));
        ...
    }
    ...
}

public abstract class AbstractUserProfileManager implements UserProfileManager {
    ...
    @Override
    public String getProfileValue(String key) {
        return getProfileValue(key, persistent);
    }
    private String getProfileValue(String key, boolean persistent) {
        return userProfileDal.getItem(keyTransfer(key, false), persistent);
    }
    ...
}

public class UserProfileDalImpl implements UserProfileDal {
    ...
    @ReadThroughSingleCache(namespace = NS, expiration = EXPIRATION)
    public String getItem(@ParameterValueKeyProvider String key, boolean persistent) {
        if (persistent) {
            MemObj memObj = memObjDao.getMemObj(key);
            return (memObj == null) ? null : memObj.getObj();
        } else {
            return null;
        }
    }
    ...
}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="dao.mybatis.common.MemObjDao">
    <select id="getMemObj" resultMap="MEM_OBJ" parameterType="String">
        select
        ID,
        OBJ,
        GMT_CREATE,
        GMT_MODIFIED
        FROM AO_MEM_OBJ
        WHERE id = #{value}
    </select>
```
出于对SQL精细控制的要求，这个项目使用了ibatis来处理数据库访问，而这样的代码，基本上都是在各个层次上传递请求，对DAO层的测试和对Controller的测试几乎是完全重叠的。实际项目中，我们也曾遇到

而且，这方面的测试一般要验证查询的各返回字段的，而需求上的变动一般会直接从controller影响到数据库表结构，也就是说，之前的测试代码，从controller到DAO全都要改动，这样剧烈变化的测试代码，其自动化的收益就大打折扣了。

的一个典型的成本就是mock/stub。

几年前有过一篇[不要把Mock当作你的设计利器]()，我查了一下，作者是ThoughtWorks的李晓，这里还有gigix转述的当时ThoughtWorks中国公司总经理郭晓的观点——

```
I did have some doubts about using Mocks when i was programming, similar reasons - too hard to refactory, too brittle. And i total agree with the three places to use it - external resources (I/O), UI, third party API.
```

我们在实际工作中很容易感受到上述文章和引论所说的痛点。无论是mock还是stub，都存在两个方面的问题——

* 对变化不友好：一旦我们进行了mock/stub，就在事实上建立了对外部变化的“屏障”，
* 推迟集成：
* 重复：

从Martin Fowler的那篇 [mock aren't stubs](http://martinfowler.com/articles/mocksArentStubs.html) 开始，很多人都讨论过这些机制，这里就不废话了，我们关注的是——它们的成本。

简单来说，mock实现简单，关注外部对象的行为将连贯的软件功能分割成了不同的部分

通常，软件开发是一个不断切分的过程——面对一个领域，首先识别目标用户粗略的需求，然后开始切分，通过需求分析和各个层面的设计，我们明确了系统、模块、类和函数，所以一般是这样的结构——

![](./images/software_layer2.png)

有些语言或者框架没有`类`、`方法`的机制，但是无论如何，“整体功能由各部分组合实现”的特点都是一样的，这个图可以去掉文字说明。

从这个角度看，所谓测试，就是对这些软件单元进行确认和验证。然而，不同层次下，验证（或者说测试）的成本和收益是不同的：

![](./images/software_layer.png)

去年我所在的团队推进质量改进时我们就发现了这个规律，当时我们首先推进的就是单元测试，虽然我强调“自动化测试”而非单元测试，但是开发同事们都很自然把精力放到了单元测试上，于是，在一段时间的热心实施以后，一些人开始出现不同的声音——“有些测试没用，发现不了问题，瞎耽误时间”

比如下面的例子：  

实践中工程师很容易发现这个问题，于是我们很快形成了一个原则——DAO层不测试，直接用Service的测试用例来覆盖DAO层的逻辑分支。



讨论持续集成对旧的单元测试思路的冲击和影响
包括在java、ruby、erlang等不同技术团队中的实践总结
