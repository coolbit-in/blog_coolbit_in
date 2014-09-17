---
layout: post
title: "C语言的格式化输入和输出"
date: 2014-09-17 21:03
comments: true
categories: C
---

##转换符

`%c` 一个字符  
`%s` 字符串  

`%d` 十进制数  
`%u` 无符号十进制数  
`%i` 和 %d 相同  

`%e` 浮点数 -e记法  
`%E` 浮点数 -E记法  
`%f` 浮点数 十进制计数法  
`%g` 浮点数 自动选择 %f 或者 %e 形式  
`%G` 浮点数 自动选择 %f 或者 %E 形式  
`%a` 浮点数 十六进制数字和 p-计数法  
`%A` 浮点数 十六进制数字和 P-计数法  

`%o` 无符号八进制数  
`%x` 小写的十六进制数  
`%X` 大写的十六进制数  
`%p` 指针  
`%%` 打印一个百分号  
<!--more-->
##实例

```c
#include <stdio.h>
#include <string.h>
int main() {
    int int_num = 0xAF3;
    double float_num = 45.1047;

    printf("*%d*\n", int_num);
    printf("*%10d*\n", int_num);
    printf("*%-10d*\n", int_num);
    printf("*%010d*\n", int_num);
    printf("*%-10d*\n", int_num);
    printf("*%-+10d*\n", int_num);

    printf("\n");

    printf("*%f*\n", float_num);
    printf("*%.2f*\n", float_num);
    printf("*%10f*\n", float_num);
    printf("*%10.2f*\n", float_num);
    printf("*%-10.2f*\n", float_num);
    printf("*%-+10.2f*\n", float_num);

    printf("\n");

    printf("%x\n", int_num);
    printf("%X\n", int_num);
    printf("%#x\n", int_num);
    printf("%#X\n", int_num);
    return 0;
}
```

##结果
```c
*2803*
*      2803*
*2803      *
*0000002803*
*2803      *
*+2803     *

*45.104700*
*45.10*
* 45.104700*
*     45.10*
*45.10     *
*+45.10    *

af3
AF3
0xaf3
0XAF3
```

