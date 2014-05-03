---
layout: post
title: "在 Fedora20 上搞 pxeboot + kickstart 全自动安装"
date: 2014-05-03 12:26
comments: true
categories: linux
---

##概述
在 Fedora20 上搞 pxe + kickstart 自动化安装需要：

* tftp-server
* xinetd
* dhcpd
* httpd
* Fedora20DVD镜像

dhcpd 做 pxe 启动的第一站：设置 ip 和指向 tftp-server
tftp-server 传递基本的内核文件
httpd 放置系统安装文件。

##在设置过程中需要注意的事件：
* 内网没有其他dhcpd的干扰
* 宿主机的 selinux iptables firewalld 处于关闭状态（尤其是firewalld，它会导致tftp传输的失效）
* 假设我们的服务端ip为 192.168.1.68

#环境搭建过程

##安装必备软件包

```bash
yum install tftp-server syslinux-tftpboot xinetd dhcp httpd
```

##设置tftpd
vim /etc/xinetd.d/tftp
```bash
# default: off
# description: The tftp server serves files using the trivial file transfer \
#   protocol.  The tftp protocol is often used to boot diskless \
#   workstations, download configuration files to network-aware printers, \
#   and to start the installation process for some operating systems.
service tftp
{
    socket_type     = dgram
    protocol        = udp
    wait            = yes
    user            = root
    server          = /usr/sbin/in.tftpd
    server_args     = -s /tftpboot
    disable         = no
    per_source      = 11
    cps         = 100 2
    flags           = IPv4
}
```
注意将其中的 `disable`键改成 `no` 这样 xinetd 才能监听 tftp 请求。
将其中的`server_args`键改成`/tftpboot`，这个目录是安装完 `syslinux-tftpboot` 后自动生成的。

##设置dhcpd
vim /etc/dhcp/dhcpd.conf
```bash
#
# DHCP Server Configuration file.
#   see x/usr/share/doc/dhcp/dhcpd.conf.example
#   see dhcpd.conf(5) man page
#
option space pxelinux;
option pxelinux.magic code 208 = string;
option pxelinux.configfile code 209 = text;
option pxelinux.pathprefix code 210 = text;
option pxelinux.reboottime code 211 = unsigned integer 32;

  subnet 192.168.1.0 netmask 255.255.255.0 {
          option routers 192.168.1.1;
          range 192.168.1.90 192.168.1.120;

          class "pxeclients" {
                  match if substring (option vendor-class-identifier, 0, 9) = "PXEClient";
                  next-server 192.168.1.68;
                  filename "pxelinux.0";
          }
  }
```

##挂载 ISO 镜像文件
```bash
cd /root
mount Fedora-20-x86-64-DVD.iso /mnt
#现在 mount 真的很智能，都不用加 -t iso9660 -o loop 参数了
```

##将必要的 boot files 复制到 /tftpboot 中
```
cp /mnt/isolinux/initrd.img /tftpboot
cp /mnt/isolinux/vmlinuz /tftpboot
cp /mnt/isolinux/boot.msg /tftpboot

mkdir /tftpboot/pxelinux.cfg
cp /mnt/isolinux/isolinux.cfg /tftpboot/pxelinux.cfg/default
```

##配置pxe启动脚本
vim /tftpboot/pxelinux.cfg/default
```bash
default vesamenu.c32
#单位是1/10秒
timeout 50 

display boot.msg

# Clear the screen when exiting the menu, instead of leaving the menu displayed.
# For vesamenu, this means the graphical background is still displayed without
# the menu itself for as long as the screen remains in graphics mode.
menu clear
menu background splash.png
menu title Fedora 20
menu vshift 8
menu rows 18
menu margin 8
#menu hidden
menu helpmsgrow 15
menu tabmsgrow 13

# Border Area
menu color border * #00000000 #00000000 none

# Selected item
menu color sel 0 #ffffffff #00000000 none

# Title bar
menu color title 0 #ff7ba3d0 #00000000 none

# Press [Tab] message
menu color tabmsg 0 #ff3a6496 #00000000 none

# Unselected menu item
menu color unsel 0 #84b8ffff #00000000 none

# Selected hotkey
menu color hotsel 0 #84b8ffff #00000000 none

# Unselected hotkey
menu color hotkey 0 #ffffffff #00000000 none

# Help text
menu color help 0 #ffffffff #00000000 none

# A scrollbar of some type? Not sure.
menu color scrollbar 0 #ffffffff #ff355594 none

# Timeout msg
menu color timeout 0 #ffffffff #00000000 none
menu color timeout_msg 0 #ffffffff #00000000 none

# Command prompt text
menu color cmdmark 0 #84b8ffff #00000000 none
menu color cmdline 0 #ffffffff #00000000 none

# Do not display the actual menu unless the user presses a key. All that is displayed is a timeout message.

menu tabmsg Press Tab for full configuration options on menu items.

menu separator # insert an empty line
menu separator # insert an empty line

label linux
  menu label ^Install Fedora 20
  #指定默认的菜单选项
  menu default
  kernel vmlinuz
  #这些参数非常的重要
  append initrd=initrd.img inst.stage2=http://192.168.1.68/ quiet ks=http://192.168.1.68/fedora20_02.cfg
```

##在GUI下生成 kickstart 脚本
```bash
yum install system-config-kickstart
```

ks脚本样例
```bash
#platform=x86, AMD64, 或 Intel EM64T
#version=DEVEL
# Install OS instead of upgrade
install
# Keyboard layouts
keyboard 'us'

# Halt after installation
halt
# Root password
rootpw --iscrypted $1$WKUQUZTR$JPqLmJGqNCWoonmMFqSKe0
# System timezone
timezone Asia/Shanghai
# Use network installation
url --url="http://192.168.1.68/"
# System language
lang en_US
# Firewall configuration
firewall --disabled
# Network information
network  --bootproto=dhcp --device=p18p1
# System authorization information
auth  --useshadow  --passalgo=sha512
# Use text mode install
text
# SELinux configuration
selinux --disabled
# Do not configure the X Window System
skipx

# System bootloader configuration
bootloader --location=mbr
# Clear the Master Boot Record
zerombr
# Partition clearing information
clearpart --all --initlabel

# Disk partitioning information
part swap --asprimary --fstype="swap" --size=2048
part / --asprimary --fstype="ext4" --grow --size=1

%packages
@standard
@virtualization

%end
```
##把kickstart脚本和ISO文件移到 http 目录下
```bash
cp -r /mnt/* /var/www/html/
cp /root/fedora20_02.cfg /var/www/html/
```

##打开相关服务
```bash
systemctl start xinetd.service
systemctl start dhcpd.service
systemctl start httpd.service
```

##关闭各种防火墙
```bash
systemctl stop iptables.service
systemctl stop firewalld.service
```

##关闭 selinux

vim /etc/selinux/config
```bash
config         semanage.conf  targeted/
[root@localhost pxelinux.cfg]# vim /etc/selinux/config
[root@localhost pxelinux.cfg]# cat /etc/selinux/config

# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     enforcing - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of enforcing.
#     disabled - No SELinux policy is loaded.
SELINUX=disabled
# SELINUXTYPE= can take one of these two values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```
把 `SELINUX` 改成 `disable`
然后重启

#参考资料
1. [PXE boot kernel and initrd fail with "Warning: /dev/root does not exist" on ProLiant BL460c G6](https://bugzilla.redhat.com/show_bug.cgi?id=1029978)
2. [PXE BOOT FEDORA 20 LINUX](http://hackerhomebrew.com/blog/?p=23)