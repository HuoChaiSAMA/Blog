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
updated: '2025-08-23T13:27:45.176+08:00'
---
## 前言

经过了短暂的等待（~~其实不那么短暂~~），终于成功地将[Qexo](https://www.oplog.cn/qexo/)部署在了[Vercel](https://vercel.com) app上，再加上前几个月达成的博客仓库远程化，现目前已经彻底告别了只能在家本地修改博客的更新方式。现在可以通过部署在**Vercel**上的**Qexo**远程修改并推送至**Github**上的远程仓库，再通过 **Github Actions** 进行自动生成与发布，即可实现自动部署，大大简化了工作量。

当然，在实现这个效果之前，**Vercel** 和 **Github Actions** 也是报了巨多的错，此处将记录过程中遇到的问题及解决方法。~~（看我记性，毕竟已经鸽了好久了:D）~~

此外，鉴于hcspace.top这个域名还没有到期，就顺便使用[全球云平台 | Cloudflare 中国官网](https://www.cloudflare-cn.com/enterprise/)的免费**CDN缓存**服务，给**Vercel**与**Github Pages**访问进行了加速。现在不用担心访问过慢了。

![Cloudflare网络服务介绍](https://s2.loli.net/2024/08/11/PvytFpw3uGVMloh.png)

另外，博客评论系统已经更新至[Twikoo | 一个简洁、安全、免费的静态网站评论系统](https://twikoo.js.org/)，现在评论不用登录Github了。~~（气死我了Edge）~~

## 一、Qexo 的 Vercel 部署

这个章节记录了在**Vercel**部署**Qexo**时我遇到的问题与解决方案。详细部署方案请参阅[Qexo (oplog.cn)](https://www.oplog.cn/qexo/)官方文档。

### 1. ./migrate.sh: line 1: pip: command not found

选择[Vercel 部署 (PostgreSQL/Vercel)](https://www.oplog.cn/qexo/start/build.html#vercel-%E9%83%A8%E7%BD%B2-postgresql-vercel)的部署方案（其他部署方案类似），在[重新部署](https://www.oplog.cn/qexo/start/build.html#%E9%83%A8%E7%BD%B2)操作遇到如下报错：

![报错图片](https://s2.loli.net/2024/08/11/ajGqbyFJm2odnCv.jpg)

```powershell
./migrate.sh: line 1: pip: command not found
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

#### 速度比较

由于**Github Pages**为静态网页，我们可以使用**CDN**来缓存`.css`和`.js`文件，来减少客户端向Github请求的文件数量。使用**Cloudflare**的CDN缓存访问测速如下：

![](https://s2.loli.net/2024/08/13/5G2JEReyx3ms19T.png)

直接访问Github.IO的测速如下：

![](https://s2.loli.net/2024/08/13/lvZH75D6NSmqVtM.png)

虽然看似直连访问更快，但也可以看到实际上**并不稳定**，成都的两个节点**全部超时**。而且还有矮墙影响，有些时候根本无法连接。因此，我们选择更稳定的**Cloduflare的CDN缓存**

#### CDN部署

详见博客：[为Github page绑定自定义域名并实现https访问-CSDN博客](https://blog.csdn.net/yucicheung/article/details/79560027)。在域名解析时，我们使用**Cloudflare**的DNS解析即可。

具体操作：

##### 1.添加解析

添加四个**A**类型解析与一个**CNAME**类型解析，如下：

![](https://s2.loli.net/2024/08/13/izc8gQdSyFJx3KR.png)

我的A类型解析是泛解析，你可以在此自定义主机名，注意**在Github上填写的是你设置A类型的域名**！！！

几个ip：

```powershell
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```powershell
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153

```

其中`www`这个主机名指向你的github.io，使得`www.example.com`也能指向你的网站。

##### 2.修改Github Pages Domian

按如图所示设置你的自定义域名：

![](https://s2.loli.net/2024/08/13/nR69t1LUpmQsSPM.png)

设置之后，**Github**会帮你在仓库根目录创建`CNAME`文件并检测你的**DNS解析**，检测成功后，你就可以通过`https://example.com`访问你的站点并启用CDN缓存了。

当然，如果你是用的**Hexo**直接**Deploy**到**Github Pages仓库**中，**Hexo**会覆盖掉仓库的所有内容导致`CNAME`文件丢失。此时在博客仓库或本地博客目录中创建`Your-Hexo-Blog-Path\source\CNAME`文件，文件中输入你的自定义域名`example.com`并保存，这样**Hexo**在每次**Generate**的时候就会把`CNAME`文件附加到public文件夹里并同时部署了。

你可以在**Cloudflare**仪表盘中的**缓存**选项中配置你的缓存计划。当你的站点更新涉及到被选中缓存的文件内容变动，但标识符未改变时，你也可以在这里删除你的CDN缓存对其进行刷新。

![](https://s2.loli.net/2024/08/13/kUWzFjBXpMgIG9C.png)

关于这方面操作涉及的实际问题，可参阅我的博客[解决CDN缓存刷新问题 - H.C.Blog (hcspace.top)](https://hcspace.top/2023/11/12/%E8%A7%A3%E5%86%B3CDN%E7%BC%93%E5%AD%98%E5%88%B7%E6%96%B0%E9%97%AE%E9%A2%98/)。该文章介绍的是**本地CDN文件**缓存刷新机制造成的问题，两者原理是相通的。但你不可能要求你的用户自行清空本地缓存文件，所以建议对文件的标识符进行版本区分。毕竟**Cloudflare**是在我们手里的。

## 结尾 & 相关链接

好了，这就是这篇博客的全部内容。

### 相关网页

{% link Vercel::https://vercel.com/::https://s2.loli.net/2024/08/13/5ScVwneNDGkTbdP.jpg %}

{% link Cloudflare::https://www.cloudflare-cn.com/::https://s2.loli.net/2024/08/13/jxnViZSXE34dmuw.png %}

{% link Qexo::https://www.oplog.cn/qexo/::https://s2.loli.net/2024/08/13/WtbqUKmGjuiNyzA.png%}

### 相关博客

[解决 Vercel 部署网站在国内被墙 | Zoey's Blog (blog-zoey.top)](https://blog-zoey.top/posts/vercel-dns-china)

[【教程】Github Page 添加自定义域名\_github pages 自定义域名-CSDN博客](https://blog.csdn.net/qq_34902437/article/details/140298754)

[为Github page绑定自定义域名并实现https访问-CSDN博客](https://blog.csdn.net/yucicheung/article/details/79560027)

[Unavailable for your site because your domain is not properly configured to support HTTPS · community · Discussion #134087 (github.com)](https://github.com/orgs/community/discussions/134087)

## 2025-8-23 更新

解决了 **Github Pages**无法勾选`Enforce HTTPS`的问题：
在**Cloudflare**的`DNS配置`中将所有的有关**Github Pages**的记录从`代理`改为`仅DNS`。待**Github Pages**启用 `Entorce HTTPS`后再改回来。

`SSL限制模式`可以更改为`完全（严格）`
