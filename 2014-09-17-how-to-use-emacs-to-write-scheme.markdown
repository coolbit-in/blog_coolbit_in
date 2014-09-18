---
layout: post
title: "如何优雅的使用emacs写scheme"
date: 2014-09-17 21:25
comments: true
categories: scheme
---

作为多年的vim用户，想一下子摆脱vim键位转移到emacs是不可能的事。但是我却越来越讨厌vim，我终于想明白了我喜欢的并不是“vim本身”，而是“多模式编辑和vim所使用的键位”，当然具体原因我以后在说。最近的目标是继续看scheme，在看了王垠的[《Scheme编程环境的设置》](http://www.yinwang.org/blog-cn/2013/04/11/scheme-setup/)之后，我决定试一试
<!--more-->
##Emacs + Evil
Evil是Emacs上的vi扩展层(Evil is an extensible vi layer for Emacs)

安装起来也比较简单，可以参考官方的[安装教程](http://www.emacswiki.org/emacs/Evil)

可以查看`evil-maps.el`，查阅所有的 key map

##Paredit
王垠的blog中推荐的 S-expression 编辑插件  
[功能键表](http://www.emacswiki.org/emacs/PareditCheatsheet)

##.emacs
```
(set-keyboard-coding-system nil)


;;Enable the evil-mode
(add-to-list 'load-path "~/.emacs.d/evil")
(require 'evil)
(evil-mode 1)
;;End


;;Enable the paredit-mode
(autoload 'paredit-mode "~/.emacs.d/paredit"
  "Minor mode for pseudo-structurally editing Lisp code."
  t)
;;;Add hooks on scheme-mode and emacs-lisp-mode
(add-hook 'scheme-mode-hook
  (lambda ()
    (paredit-mode 1)))

(add-hook 'emacs-lisp-mode-hook
  (lambda ()
    (paredit-mode 1)))
;;End


;;Show the parenthesis
(show-paren-mode 1)
;;End


;;Configure the line number
(global-linum-mode 1)
(setq linum-format "%2d  ")
;;End


;;Enable the color-theme plugin, but it dostn't work in -nw mode whatever Linux and OSX
(add-to-list 'load-path "~/.emacs.d/color-theme-6.6.0")
(require 'color-theme)
(setq color-theme-is-global 1)
(color-theme-initialize)
(color-theme-ramangalahy)
;;End


;;Use the theme 'wombat in -nw mode
(load-theme 'wombat)
;;End
```



