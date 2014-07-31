---
layout: post
title: python function
category: 语言 
tags: python php
---
#### function传递未知数量的参数
* python

```
#!/usr/bin/env python
def fun(a,*b, **c):
    print a
    print b
    print c
fun(1,2,3,4,5,m='mmmm',n='nnn')

#输出
#1
#(2, 3, 4, 5)
#{'m': 'mmmm', 'n': 'nnn'}
```
{%raw%}
* php func_get_args()
* C   void fun(int i, ...) 或 void fun(int i, *argv[])
{%endraw%}


