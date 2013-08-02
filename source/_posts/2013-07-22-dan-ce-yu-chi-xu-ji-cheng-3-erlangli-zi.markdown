---
layout: post
title: "单测与持续集成3-erlang例子"
date: 2013-07-22 10:22
comments: true
categories: erlang, 测试, 集成
---

本篇是[这篇](http://fsword.github.io/blog/2013/06/07/dan-yuan-ce-shi-bu-shi-chi-xu-ji-cheng-de-ji-chu-2-xiang-mu-zhong-de-li-zi/)的后续，很早就放进了草稿箱，但是我一直懒得修改好，真是典型的拖延症患者。

我自己在项目中使用erlang时间并不长，而且断断续续，充其量是个初学者。之所以用erlang举例子，是因为它比较有代表性。

学习erlang，OTP是个转折点，接触了gen\_server等一系列模式以后，很自然就会感觉到其实一个erlang进程更像是一个对象——有标识符，内部保存状态、对外提供服务接口、可用的交互通过消息传递进行等等。

相应的，进行测试时的问题也很类似。人们在宣传erlang时常常说它由于状态不可变，所以可测性很好，然而如果以一个进程为测试对象来看，我们会遇到进程间协作的问题——这相当于java程序里面的对象间协作。

在apposs\_agent项目中我就遇到了这类问题，例如，有三个模块之间的依赖关系如下：

    client -> responder
           -> ssh_executor

那么，这时模块client中怎么使用其它模块呢？下意识的，我们让这种依赖变得“可插入”，于是就有了类似这样的代码：

```erlang
%% client.erl
%% .....
init([Responder_mod, Host, GetHostInfoFun]) ->
  %% ...
  State = #state{host = Host,
                 get_host_info_fun=GetHostInfoFun,
                 responder_mod = Responder_mod,
                 cmds = Cmds
                },
  gen_fsm:send_all_state_event(?SERVER(Host), reconnect),
  {ok, disconnected, State}. 

%% ......
normal(do_cmd, #state{host=Host, cm=Cm, cmds=[Cmd|T_cmds], exec_mod=ExecMod, responder_mod=RespMod}=State) ->
  (RespMod:run_caller(client))(Host, Cmd),
  Handler = ExecMod:exec(Cm, Cmd),
  {next_state, run, State#state{current_cmd=Cmd, cmds=T_cmds, handler=Handler}};
%% ......
```
在state中放入复杂的数据结构，其实就是为了让ExecMod和RespMod变得可以“配置”，还单独测试client而不用依赖其它模块。

然而这种设计的成本实在太高了，实际中，exec_mod和responder_mod并不会改变，这个目标属于over design。

那么为了可测性是否有必要这么做呢？有一段时间我也不是很确定。

从结构上来看，ssh_executor和responder属于下层模块，client是它们的用户，隔离下层模块运行上层模块意义不大。从某种角度看，软件开发很像搭积木，我们做好下层模块以后就不用“隔离”它们了，要测试，带上大家一起跑是很方便的做法。

想通了这一点，上述的代码就很简单了——

```erlang
init([Host]) ->
  %% ...
  State = #state{host = Host,
                 cmds = Cmds
                },
  gen_fsm:send_all_state_event(?SERVER(Host), reconnect),
  {ok, disconnected, State}. 

%% ......
normal(do_cmd, #state{host=Host, cm=Cm, cmds=[Cmd|T_cmds]}=State) ->
  (responder:run_caller(client))(Host, Cmd),
  Handler = ssh_executor:exec(Cm, Cmd),
  {next_state, run, State#state{current_cmd=Cmd, cmds=T_cmds, handler=Handler}};
%% ......
```
显然，这样修改以后，不但代码量减少了，而且代码专注于表达业务逻辑，因而也更容易理解了，好的代码应该专注，而不是眉毛胡子一把抓，您说是不是？

有人会说，那我们难道就不要单元测试了么？不错，这正是我想说的，`单元测试的关注点应该是具有很高算法复杂度的逻辑单元，而不是复杂性都委托出去的业务模块`，对于后者，不做单元测试并不是罪过。

上述的想法也只是逻辑推演，为了更加确认，我和淘宝内部的一个erlang项目的同事做了一些了解——

```
    “hi，你们的代码写很多mock吗？“
    ”写的不多“
    ”那单元测试的时候怎么隔离呢？要在进程内部的状态里面保存关联模块么？”
    “不用，我们主要写集成测试”
    “集成测试会不会覆盖不到位？”
    “目前的功能以业务为主，逻辑不是很复杂，用集成测试就够了”
```

同事的回答和我想的一样，不过这毕竟只是少数项目，我希望能有更多的案例，大家一起交流、讨论
