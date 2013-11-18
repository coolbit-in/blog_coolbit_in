---
layout: post
title: "RHCE 课程笔记（一）"
date: 2012-03-09 13:39
comments: true
categories: linux 
---
由于班里有很多没有一点基础的同学，所以头两次讲的一些基本的概念、命令，还有安装虚拟机。对我来说是没有太大的难度，不过我还是学到了不少的东西。 

###1. 虚拟机  
虽然我习惯在linux下用virtualbox，不过RHEL里面内置的虚拟机真的非常的好用啊。由于有vmare 和 virtualbox 的经验，我也是并没有遇到什么困难。

###2. 关于安装系统的一些东西  
分区的话  
/boot    200MB  
swap 一般是内存的两倍（这是老师的原话），我原来在网上看的是和内存等大就行了，也许是桌面版对swap并不是非常的重要，企业的情况下就有所不同了。  
root密码  
老师的原话是：一定要是没有逻辑的字母和数字的组合

###3. 简单的命令  
```bash
ls -R #深度遍历输出所有的文件，这个命令可以在统计某个目录下文件的个数：ls -R |wc -l  
cd - #回到之前的目录
history #查看bash命令的使用历史  
ctrl+d #关闭terminal tab 的快捷键，不过我还是更喜欢exit 
mkdir -p #递归建立目录
rm -f #删除的时候不询问  
yum localinstall xxx.rpm #安装本地rpm包自动解决依赖问题 
grep -v #反向过滤  
rpm -ivh #安装
rpm -e #卸载  
```
###4. 简单的网络设置  
```bash
/etc/resolv.conf #储存DNS  
/etc/hosts #静态网络映射  
/etc/nsswitch #可以调整hosts 和 nds 的优先级 
/etc/sysconfig/network #储存全局gateway  
/etc/sysconfig/network-scropts/ifcfg-eth0 #第一块以太网卡配置脚本  
###
DEVICE=eth0
BOOTPROTO=none/dhcp/static
ONBOOT=yes
IPADDR
NETMASK
GATEWAY
DNS1
###
```

