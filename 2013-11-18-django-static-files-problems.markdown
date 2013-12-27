---
layout: post
title: "关于django的静态文件问题"
date: 2013-11-18 23:20
comments: true
categories: pyhton django
---
django 里面静态文件的部署是一件让像我这样的新手特别头疼的一点，django 的 `setting.py` 里面关于 "STATIC"的参数有好多，但是都没有注释到位，搞得一头雾水。后来看了一些资料，总结出一点东西（或许还有没有遇到的坑，只是现在还不知道）。
##setting.py 中关于 静态文件的参数
* STATIC_ROOT 
* STATIC_URL 
* STATICFILES_DIRS 
* STATICFILES_FINDERS

`STATIC_ROOT`是一个比较迷惑人的参数，你会发现当你无法使用静态文件的时候，不管你如何设置这个值，都没有什么用。其实这个目录是用于部署的时候使用的，设个目录指定一个位置，使用`python manage.py collectstatic`这个命令将`STATICFILES_FINDERS`中找到的静态文件和`STATICFILES_DIRS`中指定的目录中的静态文件一起归并到`STATIC_ROOT`中。这样，如果你使用 nginx 的话，你就可以将这个目录作为静态文件目录设置了。

`STATIC_URL`这个是一个可以在 django 模板中使用的变量。而且在 django 的开发过程中做的静态文件 url 路由的作用。

`STATICFILES_DIRS`这个刚才说了，指定**额外**的静态文件搜索目录。

`STATICFILES_FINDERS`中的`django.contrib.staticfiles.finders.FileSystemFinder`是用于寻找`STATICFILES_DIRS`中的静态文件的。而`django.contrib.staticfiles.finders.AppDirectoriesFinder`是用来寻找每一个App目录下名为`static`的子目录中的静态文件的（比如我的App的目录是`/my_project/my_app/`，那么把静态文件放在`/my_project/my_app/static/`下，它就能找见了）

当在开发时，运行`python manage.py runserver`， django 会自动将`STATIC_URL`前缀的 url 做静态文件服务，而且启用`STATICFILES_FINDERS`中的各种 Finder 来匹配你所需要的静态文件，一切都“服务的十分到位”。

但是当你使用 uwsgi 或者 Gunicorn 将 django 部署到生产环境的时候，上述“到位的服务”就不会给你提供了。你可以使用`python manage.py runserver --nostatic`来模拟这种“部署前的黑暗”。这时候，你就要“自食其力”了。

首先，使用`python manage collectstatic`命令调用 `STATICFILES_FINDERS`中的Finder 将静态文件归并到`STATIC_ROOT`中。然后，在`urls.py`中加上
```python
url(r'^static/(?P<path>.*)$','django.views.static.serve',{'document_root':settings.STATIC_ROOT})
```
来作为静态文件的 url 路由，处理函数`django.views.static.serve`在`STATIC_ROOT`中查找静态文件。当然，你也可以使用 nginx 的静态文件服务，毕竟这是 nginx 的拿手好戏。

##官方参考文档
https://docs.djangoproject.com/en/1.5/ref/contrib/staticfiles/
https://docs.djangoproject.com/en/1.5/howto/static-files/
https://docs.djangoproject.com/en/1.5/howto/static-files/deployment/
