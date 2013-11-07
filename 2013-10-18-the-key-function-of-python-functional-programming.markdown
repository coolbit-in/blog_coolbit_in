---
layout: post
title: "python 函数式编程的关键函数"
date: 2013-10-18 09:52
comments: true
categories: python
---
##lambda匿名函数
lambda匿名函数起到了一种函数速写的作用，快速的定义一个“一句话函数”
```python
>>> f = lambda x, y: x + y
>>> f(2, 3)
>>> 5
```
lambda 是一个表达式，返回一个函数对象，这个函数对象的引用由你来赋值，本身是没有对应的函数名的，所以才叫“匿名函数”

lambda 是可以嵌套的
例如l:
```python
(lambda x : (lambda y: x + y) (99)) (4)
```

个人觉得 lambda 这个东西对于代码的可读性还是有那么一点点的影响的，尤其是对于初学者来说，看到太多的 lambda 会不明觉厉。不过，在合适的地方使用却能减少不少代码量。

## map 函数
map函数有两个参数，第一的是函数对象，第二个是一个列表。
```python
map(function, *list)
```
简单来说就是将`list`里的元素挨个载入`function`中执行，返回一个**元组**，如果需要返回 list 的话，一般使用`list(map())`的组合。

##filter 函数
filter 也就是过滤器的意思，同样的，它也有两个参数，第一个是函数对象，第二个是一个列表，只不过第一个函数的返回值是 boolean 值，这样，后面的`list`元素依次传入前面的函数，如果返回`True`就留下来，返回`False`就去掉了。同样的由于返回值是一个**元组**，通常见到的组合是`list(filter(lambda()))`。


##reduce 函数
这个用语言还真不好解释，感觉就是用来做累加或者是累乘的，不过可以用一段简单的程序来表达一下意思。
```python
def myreduce(function, sequence):
     tally = sequence[0]
     for next in sequence[1:]:
          tally = function(tally, next)
     return tally
```
大概就是这个意思吧。
