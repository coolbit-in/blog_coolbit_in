---
layout: post
title: "纠结了一些时间的水题beads(我真是蒻爆了)"
date: 2011-10-13 11:35
comments: true
categories: algorithm
---
唉，我始终觉得编程这种东西长时间不碰手就生得要死。连这么水的题都拖了这么长的时间。

今天想了一想这道题，硬着枚举吧。。

先破环为链，题上的要求是从中间向两边搜，我为了省事就从一边搜，给两条命，遇到异色就减命（w的情况，就是先减再加。。），还有就是搜满一圈就停止。
```c
/*
ID: lsy11011
LANG: C
TASK: beads
*/
#include <stdio.h> 
//var all
int n,max,length;
char a[800],temp;
//
int procedure (int k) {
    int i,live;
    char now;
    i=k;
    live=2;
    now='o';
    while (length<n) {
        if (a[i]!=now && now!='o') {
            live--;
            if (a[i]=='w')
                live++;
        }
        if (live==0) 
            return 0;
        else {
            length++;
            if (a[i]!='w')
                now=a[i];
        }
        i++;
    }
    return 0;
}

int main () {
    freopen("beads.in","r",stdin);
    freopen("beads.out","w",stdout);
    int i;
    scanf("%d",&n);
    scanf("%c",&temp);
    for (i=1;i<=n;i++) {
        scanf("%c",&a[i]);
        a[i+n]=a[i];
    }
    //live=2;   
    for (i=1;i<=n;i++) {
        length=0;
        procedure(i);
        if (length>max)
            max=length;
    }
    printf("%d\n",max);
    return 0;
}
```