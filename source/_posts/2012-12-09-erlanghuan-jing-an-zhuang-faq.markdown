---
layout: post
title: "erlang环境安装FAQ"
date: 2012-12-09 15:46
comments: true
categories: 
---

### 如何添加 wx 支持 ###

erlang的很多GUI工具都是基于wx库的，比如: reltool，但是缺省的ubuntu环境中的 erlang 包是没有wx支持的，常见错误是这样

```
1> reltool:start().

=ERROR REPORT==== 9-Dec-2012::15:28:51 ===
ERROR: Could not find 'wxe_driver.so' in: /home/john/software/otp/lib/erlang/lib/wx-0.99.1/priv
** exception exit: {load_driver,"No driver found"}
     in function  wxe_server:start/0 (wxe_server.erl, line 64)
         in call from wx:new/1 (wx.erl, line 99)
         in call from reltool_sys_win:do_init/1 (reltool_sys_win.erl, line 140)
         in call from reltool_sys_win:init/1 (reltool_sys_win.erl, line 130)
         in call from proc_lib:init_p_do_apply/3 (proc_lib.erl, line 227)
```

有经验的人一般会尝试自己编译erlang环境，但可能会发现编译时找不到wx库。实际上，ubuntu环境一般确实会安装 libwxgtk2.8-dev 这个包，但是需要添加一个 link （参考[官方说明](http://wiki.wxwidgets.org/Installing_and_configuring_under_Ubuntu)）

```
cd /usr/include
sudo ln -sv wx-2.8/wx wx
```

另外，提醒一下，你可以在 configure 阶段验证是否支持 wx ，方法是看是否有下面的输出：

```
checking for debug build of wxWidgets... checking for wx-config... /usr/bin/wx-config
checking for wxWidgets version >= 2.8.4 (--unicode --debug=yes)... no
checking for standard build of wxWidgets... checking for wx-config... (cached) /usr/bin/wx-config
checking for wxWidgets version >= 2.8.4 (--unicode --debug=no)... yes (version 2.8.12)
```

