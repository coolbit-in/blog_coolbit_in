---
layout: post
title: "使用装饰器实现异常后自动重试"
date: 2014-03-29 23:11
comments: true
categories: python 
---
这是一道两次面试都没有做出来的题。其实第二次的时候我是可以做出来的，但是，可能是面试的时候比较紧张导致“智硬”。  
先来说说面试官非常喜欢考的装饰器。

##普通的装饰器
普通的装饰器指的是不带参数的装饰器，这时候需要封装两层，第一层用于被包装函数对象的接收，第二层用于被包装函数的参数的接收。

```python
def decorator(func):
    def wrap(*args, **kwargs):
        try:
            func(*args, **kwargs)
        except Exception:
            print "Excaption catched.."
    return wrap

@decorator
def main(n):
    print "hello %d" % n
    raise Exception
```

##带参数的装饰器
带参数的装饰器比普通的装饰器要再多一层的封装，第一层用于接收装饰器参数，第二层用于被包装函数对象的接收，第三层用于被包装函数的参数的接收。

```
def decorator(x):
    def wrap(func):
        def wwrap(*args ,**kwargs):
            for i in xrange(x):
                try:
                    func(*args, **kwargs)
                except Exception:
                    print "Excaption catched.."
                else:
                    break
        return wwrap
    return wrap


@decorator(3)
def main(n):
    print "hello %d" % n
    raise Exception
```
