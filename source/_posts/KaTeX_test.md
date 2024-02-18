---
abbrlink: ''
categories: []
date: '2023-11-26T09:22:02.845830+08:00'
description: null
excerpt:  来源：Volantis魔改教程 - DearXuan的主页(https://blog.dearxuan.com/2023/01/07/Volantis%E9%AD%94%E6%94%B9%E6%95%99%E7%A8%8B/#katex%E5%85%AC%E5%BC%8F%E6%B8%B2%E6%9F%93%E9%80%9A%E7%94%A8)  设函数f(x,y)的全微分为df(x,y)=(2...
headimg: null
keywords: null
pin: null
plugins: katex
readmore: true
tags: []
title: KaTeX渲染测试
updated: 2023-11-26T9:33:23.372+8:0
---
> 来源：[Volantis魔改教程 - DearXuan的主页](https://blog.dearxuan.com/2023/01/07/Volantis%E9%AD%94%E6%94%B9%E6%95%99%E7%A8%8B/#katex%E5%85%AC%E5%BC%8F%E6%B8%B2%E6%9F%93%E9%80%9A%E7%94%A8)(https://blog.dearxuan.com/2023/01/07/Volantis%E9%AD%94%E6%94%B9%E6%95%99%E7%A8%8B/#katex%E5%85%AC%E5%BC%8F%E6%B8%B2%E6%9F%93%E9%80%9A%E7%94%A8)

设函数$f(x,y)$的全微分为$df(x,y)=(2ax+by)dx+(2by+ax)dy$,($a$,$b$为常数),且$f(0,0)=-3,f_{x}^{'}(1,1)=3$,求$f(x,y)$

本题给的是全微分,但是可以看成两个偏微分,并且较为基础,所以放在第一题

$$
\frac{∂f}{∂x}=2ax+by,\frac{∂f}{∂y}=2by+ax
$$

直接对两个偏微分求不定积分,可以得到原函数.注意对x积分时,将y看作常数,因此最后的$+C$实际上应该写作$+g(y)$

$$
f(x,y)=ax^{2}+bxy+g(y)=by^{2}+axy+h(x)
$$

显然两者是同一个函数,因此对应的项的系数也相同,即$a=b$,对x求偏导,得到$f_{x}^{'}(1,1)=2a+b=3$,故$f(x,y)=x^2+xy+y^2-3$

```markdown
plugins:
  - katex
```


$$
解: 设需要x二氧化碳 \\
\because CO_2 + Ca(OH)_2 = CaCO_3 \downarrow + H_2O \\
s
$$
 