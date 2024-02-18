---
abbrlink: '解决CDN缓存刷新问题 '
categories:
- - Web
date: '2023-11-12T11:20:24.161267+08:00'
description: null
excerpt: 开发记录 问题描述 假如你很早之前访问过我的站点（就是去年，当时我还没有绘制站点logo，用的是原作者imsyy的logo（就是那个钟）），你就会发现：最近的更新logo和字体都没有变化  这是为什么呢？ 原因分析 因为我们的浏览器在访问一个页面时，里面的图片之类的东西一般都不会改变，所以我们的浏览器就聪明地把他们都存在了本地，（这就是缓存，cache）下一次访问直接访问本地缓存就行了，大大滴提高...
headimg: https://s2.loli.net/2023/11/12/oRp2BbrUPkT1lJ3.jpg
keywords: null
pin: null
readmore: true
tags:
- Update
title: '解决CDN缓存刷新问题 '
updated: 2023-11-12T11:55:43.764+8:0
---
# 开发记录

## 问题描述

假如你很早之前访问过我的站点（就是去年，当时我还没有绘制站点logo，用的是原作者[imsyy](https://github.com/imsyy)的logo（就是那个钟）），你就会发现：**最近的更新logo和字体都没有变化**

![](https://s2.loli.net/2023/11/12/Wj9P4hv1zCfd75H.png)

这是为什么呢？

## 原因分析

因为我们的浏览器在访问一个页面时，里面的**图片**之类的东西**一般都不会改变**，所以我们的浏览器就聪明地把他们都**存在了本地**，（这就是`缓存`，`cache`）下一次访问直接访问本地缓存就行了，大大滴提高了访问速度。
但这种没刷新的情况是什么呢？因为我们制作网页时为了引用图片更方便，所以在修改图片内容时没有换图片的文件名，这样子就不用改代码。但对于缓存来说这两个文件是**一样的**（因为名字没变），所以就不会刷新。
这个时候，我们可以使用`Ctrl+F5`来刷新缓存，这样子就刷新了。

![](https://s2.loli.net/2023/11/12/ndb5gzNxW73mVlZ.png)
但是问题结束了吗？并没有！我们再按`F5`刷新（不刷新缓存）,网页内容又变成了原来的样子！
为什么会这样呢？我们不是已经把缓存刷新至最新了吗？

## 进一步探究

这时候我们打开`开发人员工具`，找到`网络`一栏，发现了**这些东西**：

![](https://s2.loli.net/2023/11/12/tYz9RPJVMmyaNrw.png)
`履行者`一栏，解释了我们是**从哪里**得到的这些文件。如果此栏**为空**，就代表它**没有从缓存获取**（例如`hitokoto 一言`，作为**需要实时刷新**的**文字**内容，肯定不能存在缓存里面的啊）。如果此栏**有信息**，说明是**从缓存获取**的（例如我们的`logo`文件,就是从`Service Worker`获取的）
研究此栏的文字，我们发现：一共有4种缓存：

![](https://s2.loli.net/2023/11/12/SzqO8Nlo7DQw3i6.png)

> `Service Worker`
> `Memory Cache`
> `Disk Cache`
> `Push Cache`

其中，`Service Worker`的解释如图

![](https://s2.loli.net/2023/11/12/biKNLs4MS2I71l3.png)
所以，我们只需要刷新`Service Worker`里面的内容就可以解决了

## 问题解决

那为什么`ctrl+f5`之后再`F5`就不行了呢？下图展示了我们`Ctrl+F5`后获取的内容。我们发现，`memory cache`里面的内容是**最新的**。再`F5`刷新，又是从`Service Worker`里面获取的文件，而它是**旧的**。

![](https://s2.loli.net/2023/11/12/SjkWAH7zbp3LNZa.png)

![](https://s2.loli.net/2023/11/12/gaMXizbuKZT76SP.png)

所以说，`Ctrl+F5`并没有刷新`Service Worker`里面的文件，而是`memory cache`的。(或者说`Service Worker`**被无视了**）
所以说，我们只用删除`Service Worker`里面的文件进行了。文件目录：

```cmd
%appdata%\..\Local\Microsoft\Edge\User Data\Default\Service Worker\CacheStorage
```

![](https://s2.loli.net/2023/11/12/wQAEfNP53oGduUs.png)

## 尾声

故事是不是应该结束了？没有！刚刚是不是发现了一个问题：为什么`logo.png`这个文件`F5`刷新是从`Service Worker`读取的，`Ctrl+F5`又是从`memory cache`里面获取的呢？
因为`Service Worker`的优先级比`memory cache`高。有`Service Worker` 先用它，没有再用`memory cache`。而`Ctrl+F5`**无视了**`Service Worker`，就导致它仍存的是之前的文件。因为它不为空，所以就不会出现“找不到”的情况，优先级最高，也就不会去找`memory cache`拿最新的文件啦！！！

## 相关链接

[清理 Edge 浏览器 Service Worker CacheStorage 所占据的空间 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/613382188#:~:text=%25appdata%25..LocalMicrosoftEdgeUser%20DataDefaultService%20WorkerCacheStorage%20%E7%94%B1%E4%BA%8E%20Service%20workers,%E6%98%AF%E7%BD%91%E9%A1%B5%E7%BC%96%E7%A8%8B%E7%BC%93%E5%AD%98%E7%9A%84%E4%B8%80%E9%83%A8%E5%88%86%EF%BC%8C%E5%9C%A8%E6%B8%85%E7%90%86%E6%B5%8F%E8%A7%88%E5%99%A8%E7%9A%84%E7%BC%93%E5%AD%98%E3%80%81%E5%8E%86%E5%8F%B2%E8%AE%B0%E5%BD%95%E7%9A%84%E6%97%B6%E5%80%99%E6%98%AF%E4%B8%8D%E4%BC%9A%E8%A7%A6%E5%8F%8A%E8%BF%99%E4%B8%80%E9%83%A8%E5%88%86%E7%9A%84%E3%80%82%20%E5%A6%82%E6%9E%9C%E8%A6%81%E6%B8%85%E7%90%86%E6%9F%90%E4%B8%80%E4%B8%AA%E7%BD%91%E7%AB%99%E7%9A%84%20CacheStorage%EF%BC%8C%E5%B0%B1%E9%9C%80%E8%A6%81%E5%9C%A8%E5%AF%B9%E5%BA%94%E7%9A%84%E7%BD%91%E5%9D%80%E4%B8%AD%E5%94%A4%E8%B5%B7%20F12%20%E5%BC%80%E5%8F%91%E8%80%85%E5%B7%A5%E5%85%B7%EF%BC%8C%E5%9C%A8%20%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F-%E5%82%A8%E5%AD%98-%E6%B8%85%E9%99%A4%E7%BD%91%E7%AB%99%E6%95%B0%E6%8D%AE%20%E8%BF%99%E4%B8%AA%E5%9C%B0%E6%96%B9%E6%9D%A5%E6%B8%85%E7%90%86%E3%80%82)

[ctrl f5后依旧从内存拿取文件，依旧有缓存机制存在\_ctrl + f5 后仍存在disk cache memory cache-CSDN博客](https://blog.csdn.net/weixin_42476799/article/details/89950355)

[缓存存在那些位置？缓存位置可分Service Worker、Memory Cache、Disk Cache、Push Cache四种\_service worker diskchache memory cache-CSDN博客](https://blog.csdn.net/m0_37217612/article/details/107942864)
