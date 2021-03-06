---
layout: post
title: "fedora 9 下的模块编译初探"
date: 2013-07-16 11:12
comments: true
categories: linux
---
## 环境概述

Linux fedora9.virtual 2.6.25-14.fc9.i686

## 第一步 准备对应的内核代码

    cd /usr/src
    tar -zxvf linux-2.6.25.14.tar.gz 
    

## 第二步 做适当的准备工作

由于新解压的内核源码没有.config文件，这时候是无法正常编译module的。  
在内核代码目录下执行:

    make oldconfig && make prepare
    

还需要编译./scripts/下的一些文件：

    make scripts
    

## 第三步 编译module

这只是一个简单的module：  
```c mymod.c

#include <linux/module.h>
#include<linux/init.h>
#include <linux/moduleparam.h>

MODULE_AUTHOR("Coolbit");
MODULE_LICENSE("GPL");

static int nbr = 10; 
module_param(nbr, int, S_IRUGO);

static int __init hello_init(void)
{
    int i;
    for (i = 0; i < nbr; i++) 
        printk(KERN_ALERT "hello, How are you %d\n", i); 
    return 0;
}

static int __exit hello_exit(void)
{
    printk(KERN_ALERT "Goodbye");
    return 0;
}

module_init(hello_init);
module_exit(hello_exit);
```

Makefile
```makefile
obj-m := modules.o                   #要生成的模块名     
modules-objs:= mymod.o        #生成这个模块名所需要的目标文件

KDIR := /usr/src/linux-2.6.25.14/
PWD := $(shell pwd)

default:
    make -C $(KDIR) M=$(PWD) modules

clean:
    rm -rf *.o *.ko *.mod.c .tmp_versions
    
```
然后运行：

    make
    

进行编译

## 出现了一些问题

我们使用

    insmod module.ko
    

进行加载的时候报错：

    insmod: error inserting 'modules.ko': -1 Invalid module format
    

这是因为编译module所用的内核版本号和系统现在运行的内核版本号不同造成的，/var/log/message 中的信息：

    May  8 06:28:20 fedora9 kernel: modules: version magic '2.6.25.14 SMP mod_unload 686 4KSTACKS ' should be '2.6.25-14.fc9.i686 SMP mod_unload 686 4KSTACKS '
    

## 解决问题

编辑内核代码目录的Makefile

    VERSION = 2
    PATCHLEVEL = 6
    SUBLEVEL = 25
    EXTRAVERSION = -14.fc9.i686    #原本是 EXTRAVERSION = .14
    NAME = Funky Weasel is Jiggy wit it
    

然后：

    make clean 
    make scripts/
    

在到module目录下重新make一下就行了。
