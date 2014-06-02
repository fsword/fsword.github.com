---
layout: post
title: "process\_info函数参数"
date: 2014-05-26 11:38
comments: true
categories: erlang debug process\_info
---

今天想查一下erlang:process\_info函数的参数，余锋同学给了一个[链接](https://github.com/ferd/recon/blob/master/src/recon.erl)，结果看到了[recon](https://github.com/ferd/recon) 这个项目，感觉很值得关注，记录一下。

不过，如果只是了解process\_info函数有哪些key，可以直接用这种方式：

```

(server@127.0.0.1)32> erlang:process_info(pid(0,1286,0)           
(server@127.0.0.1)32> ).
[{registered_name,channel@1},
 {current_function,{gen_fsm,loop,7}},
 {initial_call,{proc_lib,init_p,5}},
 {status,waiting},
 {message_queue_len,0},
 {messages,[]},
 {links,[<0.1163.0>]},
 {dictionary,[{'$ancestors',[essh_client_sup,essh_sup,
                             <0.1103.0>]},
              {'$initial_call',{essh_client,init,1}}]},
 {trap_exit,false},
 {error_handler,error_handler},
 {priority,normal},
 {group_leader,<0.1102.0>},
 {total_heap_size,17730},
 {heap_size,6772},
 {stack_size,10},
 {reductions,15053},
 {garbage_collection,[{min_bin_vheap_size,46422},
                      {min_heap_size,233},
                      {fullsweep_after,65535},
                      {minor_gcs,1}]},
 {suspending,[]}]
```
