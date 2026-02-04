---
abbrlink: RES
categories:
- - H.C.Space
date: '2026-02-04T12:12:06.087470+08:00'
description: null
excerpt: henerii.cn 重新变成主页
headimg: https://s2.loli.net/2026/02/04/HSNgEnPAmUafirb.png
keywords: null
pin: null
plugins:
- katex
readmore: true
tags:
- Update
title: 重新启用主页
updated: '2026-02-04T12:12:07.233+08:00'
---
# henerii.cn 现已成为主页站点，博客移动到 blog.henerii.cn

{% link Henerii.CN链接::https://henerii.cn::https://s2.loli.net/2026/02/03/wnhqIXuGpEysgR9.png %}

{% link imsyy/home Github Repo::https://github.com/imsyy/home::https://github.com/imsyy/home/blob/dev/public/images/icon/apple-touch-icon.png %}

## 主页预览图片

{% gallery %}
![](https://s2.loli.net/2026/02/04/HSNgEnPAmUafirb.png)
{% endgallery %}

{% frame iphone11::https://s2.loli.net/2026/02/04/iJn6t3UvwKQqoy2.jpg::alt=手机浏览器展示(未完全适配) %}

## 功能展示

* **Aplayer** 音乐播放

{% gallery %}

![](https://s2.loli.net/2026/02/04/1Mejl2JqkXmzgxb.png)

{% endgallery%}

* 主页面音乐控件&下栏嵌入歌词

{% gallery %}

![](https://s2.loli.net/2026/02/04/mFhWITdcJlZVizL.png)

{% endgallery%}

* 个性化设置

{% gallery %}

![](https://s2.loli.net/2026/02/04/XIRYa3uMmHef5Bl.png)

{% endgallery%}

* 时间、天气显示
* ......

## 存在问题

* [二级目录访问需要强制刷新才能访问。 · Issue #289 · imsyy/home](https://github.com/imsyy/home/issues/289)

## 解决方法

将博客部署到二级域名

1. 在**Github**上新开一个Repo用于存放静态页面, eg. `Yourname/Repo`
2. 在**Settings-Pages**中启用**GithubPages**服务，并选择当前分支

{% gallery %}

![](https://s2.loli.net/2026/02/04/QEvaqXWPktIl8OZ.png)

{% endgallery %}

3. 启用后，可以在 `Yourname.github.io/Repo`访问该repo
4. 为这个repo部署二级域名解析。解析类型为CNAME，解析到`Yourname.github.io.`。注意末尾的`.`
5. 在repo中创建`CNAME`文件，内容如下

```powershell
repo.example.com
```

6. 在**Custom domain**中输入该二级域名，等待**DNS check** 后勾选**Enforce HTTPS**
