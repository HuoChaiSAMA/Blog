---
abbrlink: ''
categories:
- Web
date: '2022-11-26T09:24:32+08:00'
plugins:
- katex
readmore: true
tags:
- Hexo
title: first-page
updated: '2024-03-23T11:03:29.591+08:00'
---
hi! 这里是HuoChaiSAMA(Henry) 的第一篇Post！欢迎来到[HuoChai空间-Blog板块！](http://blog.hcspace.top/hexo/)

有时间来[H.C.Space](http://www.hcspace.top)看看哦！

本篇会对hexo的写作板块进行浅浅的练习，亦为小笔记！

<!-- more -->

# 准备工作

## 创建文章

git bash 进入blog文件夹，然后执行以下语句：

```bash
$ hexo new [布局] <标题>
```

### 布局

Hexo有3种默认布局：`post` (帖子)、`page` (页面)和 `draft` (草稿)。

> 一般写文章就建 `post`，在初始化 **about** 、 **categories** 、 **tags** 文件夹时用 `page`

## Front-matter

在创建文章后，可以在 `blog/source/_posts/` 中找到 **Hexo** 自行创建的作品文件 `XXX.md`。这个文件事md格式，意味着我们可以使用 `markdown`语法进行写作。此处也是运用 `markdown`语言写作。
找到指定文章的文件用软件打开。~~（此处用的是**Typora**）~~
在文件最上端，有这样一段文字：

```
---
title: first-page
date: 
tags: 
categories: 
---
```

这就是 **Front-matter** ，指这个文件的变量，类似于声明函数。hexo会根据此处内容在你 `$ hexo g` 时按此处内容进行页面生成、管理。具体释义如下：


| 参数              | 描述                       | 默认值              |
| ----------------- | -------------------------- | ------------------- |
| `layout`          | 布局                       | 略                  |
| `title`           | 标题                       | 文章的文件名        |
| `date`            | 建立日期                   | 文件建立日期        |
| `comments`        | 开启文章的评论功能         | true                |
| `tags`            | 标签                       |                     |
| `categories`      | 分类                       |                     |
| `permalink`       | 覆盖文章地址（什么鬼）     |                     |
| `excerpt`         | 纯文本的页面摘要（？？？） |                     |
| `disableNunjucks` | 自己去官网看               |                     |
| `lang`            | 设置语言以**覆盖自动检测** | 继承自`_config.yml` |

### 分类和标签

上文表格中提到了 `tags` 和 `categories` 两个参数，具体使用方法如下(`about`页面类似):

1. 建立文件夹

```bash
$ hexo new page "tags" //建立文件夹/页面以供Hexo储存、用户点击
```

2. 分类/标签(Front-matter)

```
categories:
  - Diary
tags:
  - PS3
  - Games
```

> 注意：**Hexo**的分类与**WordPress**不同。**Hexo**在 `categories`后写入多个分类会自动分成子类。例如:
>
> ```
>
> ```

categories:

- Diary
- Fun

> ```
> 此时**Hexo**就会生成`Diary`这个父类，在这个父类下生成`Fun`这个子类。
> ```

## 准备编译器 ~~（编辑器更合适吧？）~~

你可以用记事本打开 `.md`文件，但是用它写博非常枯燥且麻烦，还要不断刷新看效果（恼）。这里**不推荐**我**曾经**用的软件: **作业部落** 的 **Cmd Markdown**。它可以在你写作时同时看到成品效果**（实时刷新）**并且提供语法提醒~~（很少）~~。总之，**我觉得**还是**比较**好用的。
[Cmd Markdown官网](https://www.zybuluo.com/cmd/)

**推荐**我现在用的软件：**Typora**

**Typora**是**收费**软件，但~~可以破解~~。此处**维护版权**，**不教怎么破解！！！**

**不推荐！不推荐！不推荐！** 我**不打广告！！！**

---

# 开始写作！

## Markdown 语法

### 1.标题

使用 `#`表示标题，`#`写在行首，与标题内容用 `空格`隔开。`#`越多代表标题更次要。
例如：

```markdown
# 一级标题
## 二级标题
### 三级标题
#### 标题这么多不会废吗？
```

> 此处不演示

### 2.分割线

使用三个或以上的 `-`或者 ` *` 表示，且这一行只有符号，**注意不要被识别为二级标题即可**，例如中间或者前面可以加空格。
例：

```markdown
 ---
1
 ***
2
 ***
3
 *****
4
```

> ---
>
> 1
>
> ---
>
> 2
>
> ---
>
> 3
>
> ---
>
> 4

### 3.斜体、粗体、删除线

```markdown
*斜体*
**粗体**
~~删除线~~
```

> *斜体*
> **粗体**
> ~~删除线~~

### 4.超链接

```markdown
[写法1](https://www.baidu.com)
[写法2][1]
[1]: https://www.baidu.com
```

> [写法1](https://www.baidu.com)
> [写法2][1]

### 5.图片

```markdown
![cmd-markdown-logo](https://www.zybuluo.com/static/img/logo.png)
```

![cmd-markdown-logo](https://www.zybuluo.com/static/img/logo.png)

### 6.无序列表

使用 `-`、`+` 和 `*` 表示无序列表，前后留一行空白，可嵌套，例如:

```markdown
//此行留空
+ 一层
    - 二层
    - 二层
        * 三层
            + 四层
+ 一层
//此行留空
```

> + 一层
>
>   - 二层
>   - 二层
>
>   * 三层
>
>   + 四层
> + 一层

### 7.有序列表

使用 `1.` （点号后面有个空格）表示有序列表，可嵌套

```markdown
1. 一层
    1. 二层
    2. 二层
2. 一层

```

> 1. 一层
>    1. 二层
>    2. 二层

2. 一层

### 8.引用

```markdown
>妙啊
>>更妙了
>>>妙中妙
>>>>妙中妙中妙
```

> 妙啊
>
>> 更妙了
>>
>>> 妙中妙
>>>
>>>> 妙中妙中妙
>>>>
>>>
>>

### 9.行内代码块（我一般用来划重点）

```markdown
`妙`
```

> `妙`

### 10.代码块

使用四个空格缩进表示代码块，一些 IDE 支持行数提示和着色，一般使用三个 `表示，例如

```markdown
    //此行为空
    #include <bits/stdc++.h>
    using namespace std
    int main(){
        return 0;
    }
    //此行为空
```

> 效果

```c++
#include <bits/stdc++.h>
using namespace std
int main(){
       return 0;
    }
```

> 直接这样写如果中间有空行会无法识别，所以推荐下面的写法

> 这个是加了 ` ``` `的代码块

```c++
#include <bits/stdc++.h>
using namespace std
int main(){
  
  
   return 0;
}
```

> 可以在 ` ``` `后面补上程序语言以高亮
> 此处不演示qwq

### 11.表格

例:

```markdown
| 商品 | 数量 | 单价  | //表头 |
| ---- | ---: | :---: |//对齐方式。依次为：默认（左对齐）、右对齐、居中对齐
|苹果|10|\$1|
|电脑|1|\$1000|
```


| 商品 | 数量 |  单价  |
| ---- | ---: | :----: |
| 苹果 |   10 |  \$1  |
| 电脑 |    1 | \$1000 |

### 12.流程图（我不想看,照搬）

主要的语法为 ` name=>type: describe` ，其中 `type` 主要有以下几种：
1.开始和结束：`start` `end`
2.输入输出：`inputoutput`
3.操作：`operation`
4.条件：`condition`
5.子程序：`subroutine`

例

```markdown
st=>start: Start:>https://www.zybuluo.com
io=>inputoutput: verification
op=>operation: Your Operation
cond=>condition: Yes or No
sub=>subroutine: Your subroutine
e=>end

st->io->op->cond
cond(yes)->e
cond(no)->sub->io
```

> 流程图也是代码块，需在 ` ``` ` 后补上类别 `flow`。
> 上述代码效果：

```flow
st=>start: Start:>https://www.baidu.com
io=>inputoutput: verification
op=>operation: Your Operation
cond=>condition: Yes or No
sub=>subroutine: Your subroutine
e=>end

st->io->op->cond
cond(yes)->e
cond(no)->sub->io
```

更多语法参考:[流程图语法参考](http://adrai.github.io/flowchart.js/)

### 13.数学公式（未实现）

使用 `$`表示，其中一个 `$`表示在行内，两个 `$`表示独占一行。
例：

```markdown
这是一个公式 $\sum_{i=1}^n a_i=0$
这个公式不听话$$E=mc^2$$
```

> 这是一个公式  $\sum_{i=1}^n a_i=0$
> 这个公式不听话
>
> $$
> E =  mc^2
> $$

支持 **LaTex** 编辑显示，访问 [MathJax](http://meta.math.stackexchange.com/questions/5020/mahtjax-basic-tutorial-and-quick-reference)参考更多使用方法。

### 14.支持HTML标签（略）

## Hexo主题 **NexT** 杂记

### 语法

置顶(Front-matter中编辑)

```markdown
top: true
```

主页不显示全部

```markdown
//这是主页显示的内容
<!-- more -->
//这里需点击进入才能显示
```

---

# 部署

写好后可以把文章部署到站点。代码摘要如下：

```bash
$ hexo clean & hexo g & hexo d
```

详细教程看下面网页。

---

[点我进入Hexo官网写作教程](https://hexo.io/docs/writing.html)

用到的网址：
[Centos7.9宝塔搭建Hexo博客，实现本地一键自动化部署到服务器](https://blog.csdn.net/Hudiscount/article/details/127210590)
[基于阿里云服务器使用宝塔面板进行Hexo建站](https://blog.csdn.net/weixin_45284594/article/details/123721560)
[网站搭建教程（详细步骤 ）](https://blog.csdn.net/bakelFF/article/details/122226529)
[服务器宝塔一键部署Discuz论坛](https://boke112.com/post/9895.html)
[Markdown语法介绍（详细）](https://blog.csdn.net/afei__/article/details/80717153)

[1]: https://www.baidu.com
