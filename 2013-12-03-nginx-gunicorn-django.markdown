---
layout: post
title: "Gunicorn + django1.5 + nginx 的部署"
date: 2013-12-03 16:11
comments: true
categories: python django 
---
虽然西电开源社区服务区新系统上线的计划搁浅，无限期推迟，但是我还是想把一些折腾的心得写出来，不然这段时间就白浪费了。

计划中软件源同步前端的组成是nginx + Gunicorn + django，我原本是想上 uwsgi 的，结果尼玛，uwsgi 实在是太难配了，我搞了整整1天都没有搞出来，虽然肯定和我水平太烂有很大的关系，但是当看到了 Gunicorn 这个东西，真的是太好配了。而且，性能虽然没有uwsgi那么变态（毕竟人家用c写的），秒杀 fastcgi 还是轻轻松松的。Gunicorn 是纯 python 实现的，安装十分简单。
```
pip install gunicorn
```
###启动命令
```
gunicorn linux_xidian.wsgi:application -w 4 -b 127.0.0.1:9055 -D -u www-data -g www-data --chdir /srv/www/linux_xidian
```
linux_xidian.wsgi 就是你项目里面那个 wsgi.py 那个文件。  
-w 指定worker的个数
-b 监听端口  
-D deamon  
-u -g 转变身份，组  
--chdir 改变根目录
###nginx 配置
```
location /mirrors {
                proxy_pass http://127.0.0.1:9055;
                proxy_set_header Host $http_host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
   }
```
OK了，简单暴力有效。
