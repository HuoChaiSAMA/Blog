---
abbrlink: py-notes
categories:
- - OI
date: '2025-10-21T12:00:14.375041+08:00'
description: null
excerpt: null
headimg: null
keywords: null
pin: null
plugins:
- katex
readmore: true
tags:
- Python
title: Python笔记
updated: '2025-10-21T12:00:32.083+08:00'
---
## 前言

本篇为学校信息技术课上学习的**Pyhton**常用语法总结

## 正文

### 1. input && output

#### 输入

```python
# 输入单个 
a = input()
# 指定输入整形
a = int(input())
# 多个变量单行输入
a1,a2 = map(int,input().split()) # .split() 为分割符，默认为' '
# 链表输入
list1 = list(map(int,input().split())) 
list2 = list(input().split()) #无声明变量类型作用
```

#### 输出

```python
a = 1.14514
print(f"{a:.2f}") ## output： 1.14
print(f"可以支持混合输出{a}")
```

### 2. math 库

[Python取整——向上取整、向下取整、四舍五入取整、向0取整\_python 四舍五入取整-CSDN博客](https://blog.csdn.net/weixin_41712499/article/details/85208928)

[Python输入的多种情况详细解读(单行、多行、数组......）\_python单行输入-CSDN博客](https://blog.csdn.net/qq_56177238/article/details/128261959)

[Python round() 函数 | 菜鸟教程](https://www.runoob.com/python/func-number-round.html)

[Python math 模块 | 菜鸟教程](https://www.runoob.com/python3/python-math.html)

[Python 列表(List) | 菜鸟教程](https://www.runoob.com/python/python-lists.html)
