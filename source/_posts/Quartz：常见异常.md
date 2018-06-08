---
title: 'Quartz:常见异常'
tags:
  - quartz
  - java
keywords: 'quartz, 持久化， 超时'
categories: Java
abbrlink: 6ed02c97
date: 2018-06-05 09:50:32
description:
---
## 持久化模式常见异常
### 无数据源
完成持久化的配置之后，仍然无法连接到数据库，报错的内容是：`C3P0的connectionProvider`初始化失败。查了很多资料，再结合错误信息，花了半天时间，才发现：原来`quartz`默认使用了`C3P0`的数据源，然而`quartz`的依赖包中并没有`C3P0`的依赖，因此无法初始化连接器。解决方法：只要在`maven`管理的`POM.xml`中把`C3P0`的依赖加上就可以了。

### 连接超时
MySQL服务器默认的“wait_timeout”是28800秒即8小时，意味着如果一个连接的空闲时间超过8个小时，MySQL将自动断开该连接，而连接池却认为该连接还是有效的(因为并未校验连接的有效性)，当应用申请使用该连接时，就会导致异常发生。

在本文，由于我们的C3P0连接池没有配置连接有效性检查，导致quartz使用的连接线程可能超时无效。
<!--more-->
错误如下：
```
JobStoreTX - MisfireHandler: Error handling misfires: Database error recovering from misfires.

Another error has occurred 
[ com.mysql.jdbc.exceptions.jdbc4.MySQLNonTransientConnectionException: No operations allowed after connection closed. ] 
which will not be reported to listeners!
```

解决这个问题的办法有三种，但是推荐第二种——减少连接池内连接的生命周期。
#### 1. 增加 MySQL 的 wait_timeout 属性的值 
修改mysql安装目录下的配置文件 my.ini文件（如果没有此文件， 复制“my-default.ini”文件，生成“复件 my-default.ini”文件。 将“复件 my-default.ini”文件重命名成“my.ini” ），在文件中设置： 

```
wait_timeout=31536000  
interactive_timeout=31536000  
```
#### 2. 减少连接池内连接的生存周期
减少连接池内连接的生存周期， 使之小于 上一项中所设置的 wait_timeout 的值。  
此处修改Quartz的c3p0配置信息，内容如下：

**org.quartz.dataSource.NAME.validationQuery**
增加用于验证连接有效性的数据库查询语句，如`select count(*) from QRTZ_CALENDARS`

**org.quartz.dataSource.NAME.validateOnCheckout**
只有配置了`validationQuery`之后，才能设置为`true`

**org.quartz.dataSource.NAME.idleConnectionValidationSeconds**
只有配置了`validationQuery`之后，才能配置有效性查询的时间间隔，默认50s

**org.quartz.dataSource.NAME.discardIdleConnectionsSeconds**
废弃空闲连接的时间，默认是0s

参考：[Quartz官方文档](http://www.quartz-scheduler.org/documentation/quartz-2.x/configuration/ConfigDataSources.html)

#### 3. 定期使用连接池内的连接
定期使用连接池内的连接，使得它们不会因为闲置超时而被 MySQL 断开。 



