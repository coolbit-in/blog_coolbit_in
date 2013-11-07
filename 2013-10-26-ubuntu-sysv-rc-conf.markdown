---
layout: post
title: "Ubuntu12.04 使用 sysv-rc-conf 管理开机启动项"
date: 2013-10-26 23:38
comments: true
categories: linux
---
今天在管理我的VPS(Ubuntu12.04)的时候，突然发现一个我居然这么久都没有发现的问题。前年在考RHCE的时候，记得管理CentOS的自启动项使用的使用`chkconfig`这个命令就可以轻松完成，但是Ubuntu下却没有默认安装这个工具，安装后，使用的时候也有问题，`chkconfig -l`是没有问题的，但是要修改的时候就报错了，估计是编译参数不是很对，于是上网查到了`sysv-rc-conf`这个工具，是带命令行界面的，很方便，不过我觉得查找的时候还是使用`chkconfig比较方便`。
![截图](http://coolbit.in/images/sysv-rc-conf.png)

