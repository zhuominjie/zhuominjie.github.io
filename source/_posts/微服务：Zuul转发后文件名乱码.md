---
title: 微服务：Zuul转发后文件名乱码
tags:
  - zuul
  - 文件上传
  - 乱码
keywords:
  - zuul
  - 文件上传
  - 乱码
categories: 微服务
abbrlink: e7f3dec2
date: 2018-06-12 12:50:34
---
最近在使用 SpringCloud 搭建微服务的过程中，发现上传文件经过 Zuul 网关转发的时候，因为上传文件名中文乱码，导致文件的写操作失败。但是不经过 Zuul 转发的时候，文件上传正常，因此猜测是 Zuul 对上传的请求的编码进行了处理。

```
原文件名：file中文.txt
上传后：file__.txt
```
<!--more-->
## 解决方法如下：
### 一、增加请求前缀
在上传文件的请求路径之前添加字符串`zuul`声明。网关将所有带`/zuul`请求的请求都走`zuulServlet`，不带`zuul`的请求都走`spring mvc`的`dispatchServlet`，避免多余的编码处理。

```
原请求路径：http://localhost:8080/uploadFile
修改后：http://localhost:8080/zuul/uploadFile
```

### 二、增加 application.property 属性
在方案一的基础上，主要修改前端代码，也可以在`application.property`声明属性来处理。

```
zuul.servlet-path=/
```
但是，如果前端文件部署在网关上，会由于所有的请求都跳过`spring mvc`处理，而无法正常渲染前端页面。因此，请根据实际需求选择解决方案。