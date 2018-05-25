---
title: Hexo：NexT主题优化之路
tags:
  - Hexo
  - Next
  - tags
  - 文章阴影
  - 文章列表间距
  - 搜索，Pisces
categories: Hexo
keywords:
  - 文章阴影
  - 文章列表间距
  - 搜索
  - Pisces宽度
abbrlink: dc01d1e2
date: 2018-04-18 14:36:47
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
## 添加站内搜索
### 安装插件
支持站内搜索需要`hexo-generator-search`和`hexo-generator-searchdb`两个插件，在站点的根目录下执行以下命令：

```
npm install hexo-generator-search --save
npm install hexo-generator-searchdb --save
```
### 启用搜索
**第一步：**在hexo的_config.yml配置文件增加如下内容：

```
search:
  path: search.xml
  field: post
  format: html
  limit: 10000
```
**第二步：**在NexT的_config.yml配置文件修改如下内容：

```
local_search:
  enable: true
```

## 修改Pisces主题内容区宽度

默认的宽度觉得有点窄，想改宽一点，可以在`source/css/_schemes/Picses/_layout.styl`文件末尾添加如下代码:

```css
/*扩展宽度*/
header{ width: 80% !important; }
header.post-header {
  width: auto !important;
}
.container .main-inner { width: 80%; }
.content-wrap { width: calc(100% - 260px); }

.header {
  +tablet() {
    width: auto !important;
  }
  +mobile() {
    width: auto !important;
  }
}

.container .main-inner {
  +tablet() {
    width: auto !important;
  }
  +mobile() {
    width: auto !important;
  }
}

.content-wrap {
  +tablet() {
    width: 100% !important;
  }
  +mobile() {
    width: 100% !important;
  }
}
```
## 去掉图片默认的边框
将Next主题/themes/next/source/css/_common/components/post/post-expand.styl文件中的img的border的值修改为none

```
//将border的值修改为none即可去掉边框，默认值为 1px solid $gray-lighter
  img {
    box-sizing: border-box;
    margin: auto;
    padding: 3px;
    border: none; 
  }
```
## 持久化链接优化
写的文章复制到微信中链接被分开了，无法直接点击链接访问。看了其他人的博客，大多数是转换为英文的，也没说实现方法。看到一个abbrlink的方法，使用解决了这一问题。
### 安装abbr-link插件
```
npm install hexo-abbrlink --save
```
### 配置博客站点文件
```
permalink: posts/:abbrlink/
# abbrlink config
abbrlink:
  alg: crc32  #support crc16(default) and crc32
  rep: hex    #support dec(default) and hex
```
### 部署
执行`hexo clean`和`hexo s`，使得配置生效