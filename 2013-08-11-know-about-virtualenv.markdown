---
layout: post
title: "初识 virtualenv"
date: 2013-08-11 12:46
comments: true
categories: python
---
在安装 Octopress 的时候知道了 Ruby 有 RVM 这个东西，可以方便的转换不同的开发环境。而在 python 下，相似的有 virtualenv，不过由于不了解 Ruby，当时配 Octopress 的时候略有艰难，还以为 virtualenv 也是一个费神的玩意儿，所以也没有过多的了解。  
昨天看 Flask 的文档时，第一次看到有直接看到在文档中推荐使用 virtualenv，而且感觉也不是很麻烦。

###安装
```bash
$ sudo pip install virtualenv
```

###建立独立环境
```bash
$ virtualenv ~/virtualenvs/flask
```
这样`~/virtualenvs/flask/`就成了这个独立环境的根目录

###开启/关闭 独立环境
开启
```bash
$ source ~/virtualenvs/flask/bin/activate
```

关闭
```bash
$ deactivate
```

