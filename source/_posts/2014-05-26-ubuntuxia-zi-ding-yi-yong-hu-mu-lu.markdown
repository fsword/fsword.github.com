---
layout: post
title: "[编译]ubuntu下自定义用户目录"
date: 2014-05-26 12:48
comments: true
categories: linux ubuntu Desktop xdg-user-dirs
---

内容来源： [链接](https://blog.dbrgn.ch/2010/5/21/fix-ubuntu-user-directories-desktopmusictemplates-etc/)

有的linux桌面(例如ubuntu/xbuntu)会按照xdg-user-dirs规范将一些目录作为缺省目录(例如 Desktop, Music, Pictures, Templates 等等)，这些内容是可以自己修改和维护的，配置文件在 ~/.config/user-dirs.dirs

```
# This file is written by xdg-user-dirs-update
# If you want to change or add directories, just edit the line you're
# interested in. All local changes will be retained on the next run
# Format is XDG_xxx_DIR="$HOME/yyy", where yyy is a shell-escaped
# homedir-relative path, or XDG_xxx_DIR="/yyy", where /yyy is an
# absolute path. No other format is supported.
#
XDG_DESKTOP_DIR="$HOME/Desktop"
XDG_DOWNLOAD_DIR="$HOME/Downloads"
XDG_TEMPLATES_DIR="$HOME/Templates"
XDG_PUBLICSHARE_DIR="$HOME/Public"
XDG_DOCUMENTS_DIR="$HOME/Documents"
XDG_MUSIC_DIR="$HOME/Music"
XDG_PICTURES_DIR="$HOME/Pictures"
XDG_VIDEOS_DIR="$HOME/Videos"
```
关于这些 xdg user dirs ，更详细的信息参见[这里](http://www.freedesktop.org/wiki/Software/xdg-user-dirs)
