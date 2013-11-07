---
layout: post
title: "SICP笔记，复合式的求值序"
date: 2013-10-22 17:36
comments: true
categories: scheme sicp
---
正在苦读SICP，1.1.5的两种复合式的求值序：应用序和正则序，倒是挺有意思的，还有相关的习题也不错。本想自己写一些感想的，结果发现王垠的博客中的一篇文章[怎样写一个解释器](http://www.yinwang.org/blog-cn/2012/08/01/interpreter/)中对这个东西的描述比SICP上的好多了，我小小的引用一段，在此谢谢王垠大哥啦。
>求值顺序，call-by-name, call-by-value

>当解释一个程序的时候，我们可以有好几种不同的“求值顺序”(evaluation order)。这有点像遍历二叉树有好几种不同的顺序一样（中序，前序，后序）。只不过这里的顺序更加复杂一些。比如下面的程序：

```scheme
((lambda (x) (* x x)) (+ 1 2))
```
>我们可以先执行最外层的调用，把 (+ 1 2) 传递进入函数，得到 (* (+ 1 2) (+ 1 2))。所以求值顺序是：
```scheme
((lambda (x) (* x x)) (+ 1 2))
=> (* (+ 1 2) (+ 1 2))
=> (* 3 (+ 1 2))
=> (* 3 3)
=> 9
```
>但是我们也可以先算出 (+ 1 2) 的结果，然后再把它传进这个函数。所以求值顺序是：
```scheme
((lambda (x) (* x x)) (+ 1 2))
=> ((lambda (x) (* x x)) 3)
=> (* 3 3)
=> 9
```
>我们把第一种方式叫做 call-by-name (CBN)，因为它把参数的“名字”（也就是表达式自己）传进函数。我们把第二种方式叫做 call-by-value (CBV)，因为它先把参数的名字进行解释，得到它们的“值”之后，才把它们传进函数。

>这两种解释方式的效率是不一样的。从上面的例子，你可以看出 CBN 比 CBV 多出了一步。为什么呢？因为函数 `(lambda (x) (* x x))` 里面有两个 x，所以 `(+ 1 2)` 被传进函数的时候被复制了一份。之后我们需要对它的每一拷贝都进行一次解释，所以 `(+ 1 2)` 被计算了两次！

>鉴于这个原因，几乎所有的程序语言都采用 CBV，而不是 CBN。CBV 常常被叫做“strict”或者“applicative order”。虽然 CBN 效率低下，与它等价的一种顺序 call-by-need 却没有这个问题。call-by-need 的基本原理是对 CBN 中被拷贝的表达式进行“共享”和“记忆”。当一个表达式的一个拷贝被计算过了之后，其它的拷贝自动得到它的值，从而避免重复求值。call-by-need 也叫“lazy evaluation”，它是 Haskell 语言所用的语义。

>求值顺序不只停留于 call-by-name, call-by-value, call-by-need。人们还设计了很多种其它的求值顺序，虽然它们大部分都不能像 call-by-value 和 call-by-need 这么实用。

可以看到，王垠提到的CBV就是书上说的应用序，而CBN则是正则序，他还提到了call-by-need，也就是我们经常说的**惰性求值**。

##关于习题
###习题1.5
Ben Bitdiddle发明了一种检测方法，能够确定解释器究竟采用哪种序求值，他定义了下面两个过程
```scheme
(define (p) (p))
(define (test x y)
  (if (= x 0)
      0
      y))
(test 0 (p))
```
分别分析两种求值序在解释这个程序的时候，会发生什么（这两种求值序处理 if 是一样的，都是先求值条件，再选择性的解析子表达式）？  

解答：当使用应用序的时候，解释器首先看到`(test 0 (p))`，它会求`0`和`(p)`的值，可是`(p)`是循环定义，于是，程序就死了；而当使用正则序，解释器会把`test(0 (p))` =>`(if (= 0 0) 0 (p))`，然后 选择性解释 `0`，这样，就不必陷入`(p)`的循环定义了。

###习题1.6
这个题，实际上是分析的scheme普通表达式的求值和 if 表达式的不同。下面的程序是SICP中的“牛顿逐步逼近法”
```scheme
(define (square x) (* x x))

(define (average x y) (/ (+ x y) 2))

(define (good-enough? guess x)
  (< (abs(- (square guess) x))
     0.001))

(define (improve guess x) (average (/ x guess) guess))

(define (sqrt-iter guess x)
  (if (good-enough? guess x)
      guess
      (sqrt-iter (improve guess x)
                 x)))
```
这是一个递归程序，`(sqrt-iter guess x)`递归出口，就是那个第12行的那个 if 语句控制的。当 if 条件表达式 `(good-enough? guess x)`返回`#t`那么`guess`将被解释，返回`#f`的话将解释`(sqrt-iter (improve guess x) x)`形成递归。  

按照题目的要求，我们把程序改成
```scheme
(define (new-if predicate then-clause else-clause)
  (cond (predicate then-clause)
        (else else-clause)))

(define (square x) (* x x))

(define (average x y) (/ (+ x y) 2))

(define (good-enough? guess x)
  (< (abs(- (square guess) x))
     0.001))

(define (improve guess x) (average (/ x guess) guess))

(define (sqrt-iter guess x)
  (new-if (good-enough? guess x)
      guess
      (sqrt-iter (improve guess x)
                 x)))

(sqrt-iter 1 3)
```
其中，if 改成了 new-if，但是，问题出现了，在解释器眼里，new-if 只是一个普通的表达式，它的运算符是`new-if`，参数是`(good-enough? guess x)`，`guess`和`(sqrt-iter (improve guess x) x)`，因为解释器是按照应用式进行求值的，所以在展开成 cond 语句之前，必须对这3个参数求值。所以，无论 good-enough 的结果是什么， sqrt-iter 的自身调用都会执行，于是乎如同 习题1.5 的情况一样，无限递归了。

