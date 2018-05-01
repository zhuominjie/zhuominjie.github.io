---
title: hexo-next优化之路
date: 2018-05-01 13:35:22
tags: 
- Hexo
- Next
- tags
categories: Hexo
keywords: [文章阴影,文章列表间距]
---
选择了Hexo + Next完成个人博客建站之后，仍然会有很多不足之处。此时，万能的搜索引擎和git社区为我们提供了琳琅满目的解决方案。本文将陆续记录本站采用过的优化措施，以供大家参考。
<!--more-->

## 缩小首页文章列表间距并增加阴影效果
在5.1.3版本中，可以直接修改next/source/css/_custom/custom.styl文件，如下：

```javascript
// 主页文章添加阴影效果
.posts-expand {
    .post {
        margin-top: 60px;
        margin-bottom: 20px;
        padding: 25px;
        -webkit-box-shadow: 0 0 5px rgba(202, 203, 203, .5);
        -moz-box-shadow: 0 0 5px rgba(202, 203, 204, .5);
    }
}
```

## 增加标签

### 步骤一
你需要在hexo根目录的source文件夹下新建一个tags文件夹，然后在tags文件夹里面新建一个index.md文件。快捷命令为：

```shell
hexo new page "tags"
```

### 步骤二
编辑source/tags/index.md文件，内容如下：

```
---
title: "tags"
type: tags
layout: "tags"
comments: false
---
```
`title`标题不重要，自定义。`comments`为false，可以关闭本页的评论功能。

### 步骤三
编辑主题配置文件

```
nav:
  home: /
  about: /about
  tags: /tags
```

### 步骤四
文章中多个标签的添加方式如下：

```
tags:
  - tag1
  - tag2
```
或者

```
tags: [tag1, tag2]
```
## 增加分类
方法同上，区别是:

* `tags`换成`categories`;
* 分类只能有一个。

文章中增加`categories: xxx`。
