---
title: 微服务一：Zuul
tags:
  - zuul
  - micro service
  - spring cloud
keywords:
  - zuul
  - micro service
  - spring cloud
categories: Java
abbrlink: 3da87cd8
date: 2018-05-15 14:28:11
---

## Zuul代理转发
zuul代理转发的方式有两种：

1. **URL映射：**`优点`是不用对被代理的服务做任务修改，适合旧系统迁移到微服务架构时采用。过渡状态中，通常会保留旧代码继续使用，同时逐步将功能一点点迁移到新平台。旧功能的访问便可以采用这种方法，使得项目可以在用户毫无感知的情况下迁移到微服务架构。`缺点`是每增加一个服务就需要配置一条内容，不支持动态提供后端的服务。
2. **服务化映射：**在实现微服务架构时，服务名与服务实例地址的关系在eureka server中已经存在了，所以只需要将Zuul注册到eureka server上去发现其他服务，就可以实现对serviceId的映射。

<!--more-->
#### URL映射


## 客户端存在context-path
**客户端**
application.property配置

```
spring.application.name=service-cloudapp

server.context-path=/SPLD
```
说明: 直接访问路径为——`http://ip:port/SPLD/api`

**zuul**
application.property配置

```
#注册Eureka服务发现
eureka.client.serviceUrl.defaultZone: http://localhost:8060/eureka/

#转发
zuul.routes.app.path=/SPLD/**
zuul.routes.app.serviceId=service-cloudapp
```
那么访问路径是：`http://ip:port/SPLD/SPLD/api`

如何去掉重复的前缀呢？
可以在zuul中，路由后面增加配置如下：

```
zuul.routes.app.stripPrefix=false
```
## 原理
* 当`stripPrefix=true`的时候，**代理前缀**默认会从请求路径中**移除**。访问`http://zuul-ip:port/SPLD/api`重定向到`http://spld-ip:port/api`  
* 当`stripPrefix=false`的时候，会**保留代理前缀**。访问`http://zuul-ip:port/SPLD/api`会重定向到`http://spld-ip:port/SPLD/api`。 

参考文章：
[springcloud(十)：服务网关zuul](http://www.cnblogs.com/ityouknow/p/6944096.html)