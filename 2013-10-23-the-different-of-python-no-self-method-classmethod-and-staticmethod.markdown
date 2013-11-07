---
layout: post
title: "Python  no_self_method、classmethod、staticmethod的异同"
date: 2013-10-23 15:35
comments: true
categories: python
---
最近啃Python装饰器，学到了许多以前不知道的东西，特此总结一下 Python2.7 和 Python3.x 的类中*无 self 参数方法*、*内置装饰器 classmethod*、*内置装饰器 staticmethod* 的异同，这三种方法都不像普通方法一样属于实例，是和实例无关的方法。

##无 self 参数方法
###Python2.7
在类里面定义一个无 self 参数的方法的行为，在 Python2.7 中是非法的，根本没法使用，无论你是用类调用，还是用实例调用，都会产生异常。
```python
#python2.7.3
class Test():
    def no_self_method(a):
        print a

>>> Ins = Test()
>>>Test.no_self_method(3)
raceback (most recent call last):
  File "<console>", line 1, in <module>
TypeError: unbound method no_self_def() must be called with Test instance as first argument (got int instance instead)

>>>Ins.no_self_method(3)
Traceback (most recent call last):
  File "<console>", line 1, in <module>
TypeError: no_self_def() takes exactly 1 argument (2 given)
```
###Python3.x
而在 Python3.x 中，对于类来说，是可以使用的，但是实例不行。
```
#python3.3.2
class Test():
    def no_self_method(a):
        print(a)

>>>Ins = Test()
>>>Test.no_self_method(3)
3

>>>Ins.no_self_method(3)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: no_self_method() takes 1 positional argument but 2 were given
```
这是因为，在Python3.x中，把无 self 参数方法看做是一个普通的函数，只是他的命名空间还是在类里面

##@classmethod 内置装饰器
classmethod 装饰器 来标识类方法
```
class Test():
    @classmethod
    def classmethod_def(cls):
        print(cls)

>>> Ins = Test()
>>>Test.classmethod_def()
<class '__main__.Test'>

>>>Ins.classmethod_def()
<class '__main__.Test'>
```
classmethod 必须要一个参数，因为无论是用类调用还是用实例调用，都会将“类型”实例传到第一个参数里。所以类方法可以针对不同的类进行不同的操作

##@staticmethod 内置装饰器
staticmethod 和 classmethod 的不同在于，staticmethod允许没有参数，也就是说，无论是类还是实例在调用它的时候，不会像 classmethod 一样，接收到“类型”实例。
