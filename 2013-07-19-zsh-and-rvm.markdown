---
layout: post
title: "rvm 和 octopress 在 zsh 下的一些异常"
date: 2013-07-19 11:35
comments: true
categories: linux zsh
---
###将在homebrew安装的zsh设定为login shell的方法  
系统默认的 `/bin/zsh` 版本比较老,在 homebrew 安装新版的 zsh 但是却在 `/usr/local/bin/zsh` ，用 `chsh` 命令的时候会出现错误：
```
chsh: /usr/local/bin/zsh: non-standard shell
```
这是因为 `/etc/shells` 里面没有 `/usr/local/bin/zsh` 系统认定这是一个非标准的 shell ，所以拒绝更改。
```
sudo echo "/usr/local/bin/zsh" >> /etc/shells
```
然后再执行 `chsh` 就成功了

###当 shell 不是以 `--login` 参数启动时的执行 rvm 的报错问题
```
..[$] <()> zsh
..[$] <()> rvm use 1.9.3

RVM is not a function, selecting rubies with 'rvm use ...' will not work.

You need to change your terminal emulator preferences to allow login shell.
Sometimes it is required to use `/bin/bash --login` as the command.
Please visit https://rvm.io/integration/gnome-terminal/ for a example.
```
当使用了非登陆状态的 shell 的时候 系统环境不会加载 `/etc/profile*` 的一系列配置文件，所以会产生一些异常。当然你可以把 `/etc/profile*` 的内容写进 `~/.rshrc` 等 shell 的默认的配置文件。不过有更好的解决方法：
```
..[$] <()> zsh --login
..[$] <()> rvm use 1.9.3
Using /usr/local/rvm/gems/ruby-1.9.3-p448
```
可见，加上 `--login` 参数就能使 shell 加载 `/etc/profile*` 

###在 zsh 下玩 octopress 使用 rake new_post["xxx"] 命令报错的问题
```
..[$] <( (git)-[master]-)> rake new_post["xxx"]
zsh: no matches found: new_post[xxx]
```
正确的做法是：
```bash
rake new_post\["zsh and rvm"\]
```
因为 zsh 会转义 []  
不过也可以在 `~/.zshrc` 中加入：
```bash
alias rake="noglob rake" 
#noglob用来取消转义。
```

