---
layout: post
title: "让bash提示符显示彩色的git_branch"
date: 2012-04-17 16:06
comments: true
categories: bash,color,PS1 
---
之前一直很羡慕zsh用户，今天终于让bash也能显示彩色branch了
具体做法：修改你的 .bashrc，添加下面几句就可以了
```  
parse_git_branch() {
  git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/[\1]/'
}                    
_PS1=$PS1                                                 
PS1="\[\e[36m\]\u:\[\e[32m\]\w\[\e[33m\]\$(parse_git_branch)\[\e[0m\]\$ "
```                                                       
很简单，不过之前遇到了点麻烦：添加了彩色支持以后发现对较长的命令行会显示错误，后来发现是把`\[\e[36m\]`写成了 `\[\e[36m`（丢了后面的部分），汗
