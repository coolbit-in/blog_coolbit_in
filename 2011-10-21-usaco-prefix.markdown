---
layout: post
title: "USACO 2.3.1 prefix"
date: 2011-10-13 11:35
comments: true
categories: algorithm
---
最近一直处于乱刷刷的状态，突然间想搞下字符串匹配，就搞起了这道题。先是在KMP算法上纠结了好长时间（2天。。）恩，网上找来找去最后还是回归于《算法导论》（算导就是神器啊），搞好了模板，然后开始看题。。想了想有一点思路，偶然发现qinz’的blog上说用朴素算法也可以过。。shit，然后就打了。。用了milk2的独创思想，效率应该比qinz的好一点，没有用KMP优化，看了一下数据，其实也优化不了多少。。然后纠结于eof的判断，TMD我又找到了一个转C++的理由，C的结束判断实在是比较诡异。。无奈自己打了一个逐字读入的循环，总算是勉强过了。。（其中还有一次把数据范围开错，shit）。。。

其实收获还是挺多的，KMP总算是懂了，啥时候在把KMP加进去练练手，额，结束，祝我月赛好运！
```c
/*
ID: lsy11011
LANG: C
TASK: prefix
*/
#include <stdio.h>
#include <string.h>
#include <memory.h>
short v[2000001];
char a[201][20];
char t[2000001];
int l_t;
void judge (int x) {
    int i,j;
    int l_a,temp;
    l_a=strlen(a[x]);
    for (i=0;i<=l_t-l_a;i++) {
        temp=1;
        for (j=0;j<=l_a-1;j++) 
            if (a[x][j]!=t[j+i]) {
                temp=0;
                break;
            }
        if (temp==1) {
            v[i]+=1;
            v[i+l_a]-=1;
        }
    }
}
int main () {
    freopen("prefix.in","r",stdin);
    freopen("prefix.out","w",stdout);
    memset(v,0,sizeof(v));
    int i;
    int n,str;
    char ch;
    i=0;
    while (i++,scanf("%s",a[i]),a[i][0]!='.') {
    }
    n=--i;
    i=0;
    while ((ch=getchar())!=-1) {
        if (ch!='\n') {
            t[i]=ch;
            i++;
        }
    }
    t[i]='\0';
    l_t=strlen(t);
    //input end
    for (i=1;i<=n;i++) {
        judge(i);
    }
    str=0;
    for (i=0;i<=l_t;i++) {
        str+=v[i];
        if (str==0 ) {
            printf("%d\n",i);
            break;
        } 
    }
    return 0;
}
```