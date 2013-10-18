---
layout: post
title: "Codeforces Beta Round #95 (Div. 2) B题 引发的一些思考"
date: 2011-11-26 00:01
comments: true
categories: algorithm
---
第一次参加codeforces，只是熟悉了一下比赛的模式。比赛的时间内只AC了A题，在B题卡题了，一直WA一个点，X轩说是精度的问题，我把res变量开到long long ，还是WA，我随即检查了算法，发现没有问题，于是纠结万分。

结束的第2天，在群里梁神说可以看数据，我把卡到的第八个点看了一下，是一个极限数据，我的变量还是爆了。明明res是long long 类型，但是为什么结果会爆呢，代码中有这么一句话: `res+=(a[i+10]*a[10-i]); `我发现我的a[]数组开的是int ，那么我很快意识到一个我编了这么多年程序都一直这忽视的一个情况,假设有两个int 变量 a=50000 b=50000 ，如果执行 `printf(“%d”,a*b);` 这个很明显会爆i`a*b`的结果已经超过了int 的范围，于是我们 声明一个long long 类型的变量c ，`c=a*b; printf(“%lld”,c); `结果呢，没有如愿得到2500000000的答案，还是爆了，而且结果跟直接输出`a*b`没有区别，说明 `a*b` 的时候就已经错了，原因`(a*b)`的返回值是int 类型，在计算a*b的时候就溢出了，任凭你在用long long 装他，他已经错了。那么`c=(long long)a*b;`这样就可以了，将long long 与 int 相乘 返回值是 long long，这个样子就可以装下2500000000了（当然a和b直接long long 也可以 我就是这么改b题才过的）。

正是由于这个错误，导致B题无限卡死，恍然大悟后我觉得有些不可思议，这个看似非常简单的问题，我以前却从来都没有遇到过。强类型语言的细节的确要注意。
```c
#include <stdio.h>
#include <memory.h>
#include <iostream>
using namespace std;
long long a[201];
int main () {
        int n,i,x;
        long long res;
        memset(a,0,sizeof(a));
        //scanf("%d",&n);
        cin>>n;
        for (i=1;i<=n;i++) {
                //scanf("%d",&x);
                cin>>x;
                a[x+10]++;
        }
        res=0;
        for (i=1;i<=10;i++) {
                res+=(a[i+10]*a[10-i]);
        }
        //for (i=1;i<a[10];i++)
        res+=(a[10]*(a[10]-1)/2);
        //printf("%I64d\n",res);
        cout<<res<<endl;
        return 0;
}
```