---
abbrlink: Qexo的Vercel部署与Cloudflare使用
categories:
- - H.C.Blog
date: '2024-08-11T21:26:29.087278+08:00'
description: Qexo的Vercel部署与Cloudflare使用
excerpt: Qexo的Vercel部署与Cloudflare使用
headimg: https://s2.loli.net/2024/08/11/9yM62QdThgpjfe8.jpg
keywords: null
pin: null
plugins:
- katex
readmore: true
tags:
- Update
- Qexo
title: 走向远程化
updated: '2024-08-12T09:49:30.620+08:00'
---
## 前言

经过了短暂的等待（~~其实不那么短暂~~），终于成功地将[Qexo](https://www.oplog.cn/qexo/)部署在了[Vercel](https://vercel.com) app上，再加上前几个月达成的博客仓库远程化，现目前已经彻底告别了只能在家本地修改博客的更新方式。现在可以通过部署在**Vercel**上的**Qexo**远程修改并推送至**Github**上的远程仓库，再通过 **Github Actions** 进行自动生成与发布，即可实现自动部署，大大简化了工作量。

当然，在实现这个效果之前，**Vercel** 和 **Github Actions** 也是报了巨多的错，此处将记录过程中遇到的问题及解决方法。~~（看我记性，毕竟已经鸽了好久了:D）~~

此外，鉴于hcspace.top这个域名还没有到期，就顺便使用[全球云平台 | Cloudflare 中国官网](https://www.cloudflare-cn.com/enterprise/)的免费**CDN缓存**服务，给**Vercel**与**Github Pages**访问进行了加速。现在不用担心访问过慢了。

![Cloudflare网络服务介绍](https://s2.loli.net/2024/08/11/PvytFpw3uGVMloh.png)

## 一、Qexo 的 Vercel 部署

这个章节记录了在**Vercel**部署**Qexo**时我遇到的问题与解决方案。详细部署方案请参阅[Qexo (oplog.cn)](https://www.oplog.cn/qexo/)官方文档。

### 1. ./migrate.sh: line 1: pip: command not found

选择[Vercel 部署 (PostgreSQL/Vercel)](https://www.oplog.cn/qexo/start/build.html#vercel-%E9%83%A8%E7%BD%B2-postgresql-vercel)的部署方案（其他部署方案类似），在[重新部署](https://www.oplog.cn/qexo/start/build.html#%E9%83%A8%E7%BD%B2)操作遇到如下报错：

![报错图片](https://s2.loli.net/2024/08/11/ajGqbyFJm2odnCv.jpg)

```powershell
./migrate.sh: line 1: pip: command not found
```

已解决。详见[Vercel 部署 (PostgreSQL/Vercel) Redeploy 时报错：./migrate.sh: line 1: pip: command not found · Issue #524 · Qexo/Qexo (github.com)](https://github.com/Qexo/Qexo/issues/524)

解决方案：将**nodejs**版本降至`18.x`

详细步骤：**Vercel** -> 你的**Qexo Project** -> **Settings** -> **General** -> **Node.js Version** -> 将`20.x`改为`18.x`

然后**Redeploy**，问题解决。

## 二、Cloudflare 网络服务（CDN）

### 1. Vercel App 无法访问 & Github Pages 访问过慢

由于 **Vercel** 和 **Github** 服务器都在国外，导致在国内访问有那么一点点麻烦。**Vercel** 有专门开的解析ip，而**Github Pages** 作为静态网页可以通过**CDN服务器**达到加速访问，这两个需求恰好可以通过**Cloudflare**的网络服务达成。下面介绍**Cloudflare**服务开通全过程。

### 2. 开通Cloudflare服务

注册**Cloudflare**账号后，在**Dashboard侧边栏**选择**网站**服务，在随后的页面输入你购买好的域名。（不要加主机名！！！）

![输入你的域名，注意不要加www](https://s2.loli.net/2024/08/12/EB8IhWjlG4a5Hsz.png)

接下来，选择**Free计划**，并开通服务。

![选择计划面板](https://s2.loli.net/2024/08/12/VqGKiHjongZd7JD.png)

接下来的页面为**DNS解析**管理和**DNS服务器**修改指引，在你的**域名管理后台**处的**DNS服务器**修改为**Cloudflare**提供的服务器地址，使**Cloudflare**完全**接管**你的域名的DNS。到此，**Cloudflare服务**开通完成。

![配置你的DNS服务器](https://s2.loli.net/2024/08/12/KV1cObvD5xioeCH.png)

### 3. 为 Vercel App 加速

添加**A类型**DNS解析，如`xxx`（也可以是泛解析），将内容填写为`76.223.126.88`。这是**Vercel**的官方加速ip。

![添加DNS解析](https://s2.loli.net/2024/08/12/ewucJRTApG3Q4hi.png)

然后，在你的**Vercel项目设置**里找到**Domains**设置项，在此处填写你解析好的域名，如`xxx.example.com`。

![为Vercelapp配置域名](https://s2.loli.net/2024/08/12/o2g4YudZtQxnVGB.png)

最后，你可以在**Cloudflare**中把**SSL/TLS模式**改为**严格**，防止多次重定向导致无法访问。但是这个操作会导致**Github Pages**如果没有开启**Enforce HTTPS**就无法访问，保持**完全**选项即可。

![SSl/TLS模式](https://s2.loli.net/2024/08/12/Ihp6d3KGFMZw2g8.png)

到此，你可以使用`xxx.example.com`访问你的**vercel app**了。继续配置Qexo即可。

### 4. 为Github Pages加速

上课去了，过会儿写。