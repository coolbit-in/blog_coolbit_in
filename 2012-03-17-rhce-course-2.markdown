---
layout: post
title: "RHCE 课程笔记（二）"
date: 2012-03-17 13:39
comments: true
categories: linux 
---
* system-config-lvm 可视化管理lvm
grub 下进入init 1  
e-\>choose kernel..-\>e-> 1 or s-\>enter-\>b  
getenforce   
setenforce 0  
passwd  
[相关资料](http://renlifeng.blog.51cto.com/2076113/638666)

* 救援实例 /bin/mount lost
插入光盘进入救援模式  
chroot /mnt/sysimage/ 切换到本地root  
touch /bin/mount 建立丢失文件  
rpm -qf /bin/mount 查找丢失文件所属的软件包  
exit 回到内存root  
mkdir /mnt/cd  
mount /dev/scd0 /mnt/cd  
cd /mnt/cd/Packages/  
rpm -ivh utilxxxxxx.rpm –root=/mnt/sysimage/ –force 指定本地root 加强制  
chroot /mnt/sysimage/  
file /bin/mount 查看/bin/mount 类型，确认正确安装  
exit  
reboot  
