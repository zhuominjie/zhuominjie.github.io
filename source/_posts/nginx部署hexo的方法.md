---
title: Hexo系列三：Nginx独立部署Hexo的方法
date: 2018-05-01 13:35:22
tags: [hexo, nginx]
categories: Hexo
keywords: [hexo, next, nginx, nohup, root, alias]
---

## 前言
在自己的生产环境中部署hexo静态博客，通常有两种方法：
**nohup命令**：通过`nohup`执行`hexo s`实现。由于hexo s是框架提供的调试方法，不是部署方式，因此在生产环境会存在性能问题，不建议使用；
**Nginx服务器**：通过`nginx`部署静态资源，将本地调试好的hexo工程打包生成的`public`目录部署到`nginx`上。`nginx`性能好，访问速度快。

<!--more--> 

## Hexo
### 配置

**处理二级目录**：当生产环境中，静态博客部署在二级目录下（如：“http://域名(ip)/blog”这种情况），需要修改hexo工程下的_config.yml配置文件，否则打包生成的css、js文件目录会缺失（默认在根目录），导致无法加载样式。一般修改`root`和`url`,增加二级目录

```yml
# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' 
## and root as '/child/'
url: http://yoursite.com/blog
root: /blog
permalink: :year/:month/:day/:title/
permalink_defaults:
```

**部署在根目录无需处理**

**打包**：通常在调试环境无需打包，修改后使用`hexo s`，即可生效，可以直接在本地查看效果。但是，以静态资源的方式部署需要打包生成静态资源，命令为：`hexo generate`。

## Nginx
### 配置静态资源路由
**示例**：hexo打包完成之后，以静态资源的方式部署到nginx，增加一个`location`模块。路由的细节有两种：`root`和`alias`，主要区别就是怎么解析location后面的uri。以下代码以`root`为例：
	
```
location /blog {
	root html;
	index index.html;
}
```
	
**root规则:** 以上的示例，说明访问的实际路由为：html/blog/index.html
	
**alias规则:** 同样的路径，alias需要按下面这么写，location后面的blog不会接到alias后面，而且alias指定的目录名后面一定要加上"/"。（`^~`表示uri以某个常规字符串开头，用于匹配url路径（而且不对url做编码处理，例如请求/static/20%/aa，可以被规则^~ /static/ /aa 匹配到（注意是空格））。下面是alias示例：
	
```
location ^~ /blog/ {
	alias html/blog/;
	index index.html;
}
```

### Nginx常用命令
* 启动：nginx
* 停止：nginx -s stop
* 重启：nginx -s restart
* 指定配置文件启动：nginx -c 路径 

### 参考：
1. [nginx之location（root/alias）](http:\\www.baidu.com)