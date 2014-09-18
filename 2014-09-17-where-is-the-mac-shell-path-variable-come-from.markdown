---
layout: post
title: "Mac下shell的PATH变量从哪里来?"
date: 2014-09-17 21:51
comments: true
categories: OSX
---

PATH 基本上是最容易受到关注的shell环境变量  
我在Mac下使用zsh，zsh中的PATH变量是怎么来的呢？
<!--more-->
```bash
$> echo $PATH
/Users/liusenyuan/computer/python/virtualenvs/global/bin:/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin:/opt/X11/bin:/usr/texbin:/usr/local/rvm/bin:/Users/liusenyuan/jython2.7b1:/Applications/Racket v5.3.6/bin:/usr/local/Cellar/iftop/1.0pre2/sbin:/Users/liusenyuan/computer/node.js/node_modules/less/bin
```
PATH变量的显示是用`:`隔开的，于是我们
```bash
$> echo $PATH | python -c "for i in raw_input().split(':'): print i"
/Users/liusenyuan/computer/python/virtualenvs/global/bin
/usr/bin
/bin
/usr/sbin
/sbin
/usr/local/bin
/opt/X11/bin
/usr/texbin
/usr/local/rvm/bin
/Users/liusenyuan/jython2.7b1
/Applications/Racket v5.3.6/bin
/usr/local/Cellar/iftop/1.0pre2/sbin
/Users/liusenyuan/computer/node.js/node_modules/less/bin
```
zsh 在最后加载的是 `~/.zshrc` 这个文件
```bash
source $ZSH/oh-my-zsh.sh
source ~/computer/python/virtualenvs/global/bin/activate
export PATH=$PATH:/Users/liusenyuan/jython2.7b1:/Applications/Racket\ v5.3.6/bin:/usr/local/Cellar/iftop/1.0pre2/sbin:/Users/li
senyuan/computer/node.js/node_modules/less/bin
```
开起virtualenv的时候，会在PATH最前面加上他的地址，是`/Users/liusenyuan/computer/python/virtualenvs/global/bin`。  
`export PATH=$PATH:...`加上了自定义的几个目录  
那么中间这些目录的路径是怎么来的呢?  
由于在MAC下没有strace这么个神器，在龙芯下strace了一下，zsh在启动的时候，加载了`/etc`下的 `zprofile` `zshenv`这些文件  

在/etc/zshenv
```bash
if [ -x /usr/libexec/path_helper ]; then
	eval `/usr/libexec/path_helper -s`
fi
```
运行了一个/usr/libexec/path_helper的文件。  
然后我搜到了[这个资料](http://www.softec.lu/site/DevelopersCorner/MasteringThePathHelper)

path_helper 是加载的这里面的地址
```bash
$> cat /etc/paths
/usr/bin
/bin
/usr/sbin
/sbin
/usr/local/bin
```
然后就真相大白了
