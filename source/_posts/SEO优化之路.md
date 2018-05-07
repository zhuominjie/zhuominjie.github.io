---
layout: hexo
title: SEO优化之路
date: 2018-04-30 15:03:24
tags: [Hexo, SEO]
categories: Hexo
keywords: [hexo, SEO, 百度站长平台, Google Search Console, hexo-baidu-url-submit]
---
个人博客搭建完成，就算在互联网的世界里安了一个家。从阿里云的万网申请到了域名，我们的家也就有了门牌号。然而，要在茫茫人海中被人发现，仅仅靠口口相传是不够的。我们需要在黄页上登记自己的住址和成员，这样，有缘人才能登门拜访。
<!--more-->

# 百度
## 1.生成sitemap
针对百度和谷歌，分别有两种hexo插件，hexo-generator-sitemap是传统的sitemap，可供谷歌使用；hexo-generator-baidu-sitemap则是针对百度。

```
npm install hexo-generator-sitemap --save-dev
npm install hexo-generator-baidu-sitemap --save-dev
```
安装完成后，重启hexo，执行**hexo g**后，在public目录下生成对应的xml文件。本地可以通过`http://127.0.0.4000/sitemap.xml `和 `http://127.0.0.4000/baidusitemap.xml`访问到sitemap文件。
## 2.注册百度站长平台
有百度账号即可
## 3.添加个人站点
进入站点管理，添加网站，主要障碍在第二步的**验证**，方式有三：文件、html标签和cname。由于hexo会在生成编译文件的过程中，修改html文件内容，导致百度验证失败，因此，不建议再踩一遍这个坑。

由于域名是我在万网上注册的，所以选择cname的方式。过程如下：

* 进入万网云解析管理平台；
* 添加解析>记录类型（CNAME），填写表单，两项必填：
	* **主机**：就是他给你的带有自身网站后缀的域名
	* **记录值**：ziyuan.baidu.com

## 4.提交sitemap
回到链接提交处，选择自己的站点网址。找到自动提交，选择sitemap，按照提示的格式添加自己的sitemap文件
## 5.新增蜘蛛协议
新建robots.txt文件，添加以下文件内容，把robots.txt放在hexo站点的source文件下。

```
# hexo robots.txt
User-agent: * Allow: /
Allow: /archives/
Disallow: /vendors/
Disallow: /js/
Disallow: /css/
Disallow: /fonts/
Disallow: /vendors/
Disallow: /fancybox/

Sitemap: http://dadroid.cn/sitemap.xml
Sitemap: http://dadroid.cn/baidusitemap.xml
```
然后去百度站长平台检测`robots`文件是否生效。

## However
挂了好几天，发现百度依然收录不了我的站点，登录平台查看`抓取诊断`->`抓取一次`，错误信息如下：

```
HTTP/1.1 403 Forbidden
Cache-Control: no-cache
Content-Type: text/html
Transfer-Encoding: chunked
Accept-Ranges: bytes
Date: Thu, 03 May 2018 05:57:37 GMT
Via: 1.1 varnish
Connection: close
X-Served-By: cache-hnd18744-HND
X-Cache: MISS
X-Cache-Hits: 0
X-Timer: S1525327058.780403,VS0,VE113
Vary: Accept-Encoding
X-Fastly-Request-ID: 7333aaaa3853b41672517dffa1a85e843dcbcdb4
```

可以看出该错误是`拒绝访问`，根据百度提供的信息可知

{% blockquote %}
【访问遭拒绝】
一般情况下，百度会通过跟踪网页间的链接来查找内容。百度spider必须能够访问某个网页才能抓取该网页。如果您意外地看到了“访问遭拒”错误，可能是由于以下几种原因导致的：
（1）百度spider无法访问您网站上的网址，因为您网站上的所有或部分内容要求用户登录后才能查看。
（2）您的服务器要求用户使用代理进行身份验证，或者您的托管服务提供商阻止百度spider访问您的网站。
{% endblockquote %}

说明我们托管在`github pages`上的博客禁止百度爬虫的访问。那么我们有什么办法能让百度收录我们的页面呢？

* 托管在国内平台，如coding
* 采用主动/手动提交链接

由于coding绑定自定义域名免费模式会被拦截，显示coding的广告，既影响爬虫抓取站点内容，也影响美观，因此尝试过后便放弃了。下面介绍使用hexo自动提交链接的插件。

### 前提
注册百度站长工具，然后在工具->网页抓取->链接提交里找到你的密匙。

### hexo-baidu-url-submit
首先，在Hexo根目录下，安装本插件：
`npm install hexo-baidu-url-submit --save`

然后，同样在根目录下，把以下内容配置到_config.yml文件中:

```
baidu_url_submit:
  count: 1 ## 提交最新的一个链接
  host: www.hui-wang.info ## 在百度站长平台中注册的域名
  token: your_token ## 请注意这是您的秘钥，所以请不要把博客源代码发布在公众仓库里!
  path: baidu_urls.txt ## 文本文档的地址， 新链接会保存在此文本文档里
```

其次，记得查看_config.ym文件中url的值， 必须包含是百度站长平台注册的域名（一般有www）， 比如:

```
# URL
url: http://www.dadroid.cn
root: /
permalink:
```

最后，加入新的deployer:

```
deploy:
- type: git ## 这是我原来的deployer
- type: baidu_url_submitter ## 这是新加的
```

### 实现原理
* 新链接的产生，`hexo generate`会产生一个文本文件，里面包含最新的链接
* 新链接的提交，`hexo deploy`会从上述文件中读取链接，提交至百度搜索引擎

# 谷歌
步骤1——5与百度大同小异，以下介绍一些不同点：
## 1.注册Google Search Console
链接：https://www.google.com/webmasters/
## 2.抓取方式
完成robost检测后，点击左侧的`Google抓取方式`。

在这里我们填上我们需要抓取的url，不填这表示抓取首页，抓取方式可以选择桌面，智能手机，自行根据需要选择。填好url之后，点击抓取。
然后可能会出现几种情况，如:完成、部分完成、重定向等，自由这三种情况是可以提交的。

提交完成后，提交至索引，根据提示操作就可以了

# hexo优化
## 修改文章链接

Hexo默认的文章链接形式是一个四级url——`domain/year/month/day/postname`，可能造成url过长，对搜索引擎是十分不友好。我们可以改成`domain/postname`的形式，编辑站点_config.yml文件，修改permalink字段改为`permalink: :title.html`即可。
## keywords 和 description
在hexo工程根目录下的`\scaffolds\post.md`中添加如下代码，用于生成的文章中添加关键字和描述。

```
keywords:
description:
```
## 给出站链接添加 “nofollow” 标签
网络爬虫可能在搜索当前页面的所有链接时，跳到别的网站回不来了。因此，需要`nofollow`标签发挥作用。

`nofollow`标签是由**谷歌**领头创新的一个“反垃圾链接”的标签，并被百度、yahoo等各大搜索引擎广泛支持，引用`nofollow`标签的目的是：用于指示搜索引擎不要追踪（即抓取）网页上带有`nofollow`属性的任何出站链接，以减少垃圾链接的分散网站权重。

Hexo的Next主题需要改以下几个地方：

1. 找到`footer.swig`，路径在`your-hexo-site\themes\next\layout\_partials`，将下面代码中的`a标签`加上`rel="external nofollow"`属性；

```
{{ __('footer.powered', '<a  class="theme-link"  href="http://hexo.io">Hexo</a>') }}
```

```
<a class="theme-link" href="https://github.com/iissnan/hexo-theme-next">
```
2. 修改`sidebar.swig`文件，路径在`your-hexo-site\themes\next\layout_macro`，将下面代码中的`a标签`加上`rel="external nofollow"`属性；

```
<a href="{{ link }}" target="_blank">{{ name }}</a>
```

```
<a href="http://creativecommons.org/licenses/{{ theme.creative_commons }}/4.0" class="cc-opacity" target="_blank">
```

## 首页title的优化
更改`index.swig`文件，文件路径是`your-hexo-site\themes\next\layout`，将下面代码：
```
{% block title %}  {{ config.title }}  {% endblock %}
```
改为：
```
{% block title %}  {{ config.title }} - {{ theme.description }}  {% endblock %}
```