---
title: 'RabbitMQ:配置远程访问'
abbrlink: e4d74468
date: 2018-08-20 16:13:21
tags:
keywords:
description:
---

## 开启WEB管理

如果只从命令行操作RabbitMQ，多少有点不方便。幸好RabbitMQ自带了web管理界面，只需要启动插件便可以使用。控制台输入以下命令：

```
sudo rabbitmq-plugins enable rabbitmq_management
```
响应如下：

```
The following plugins have been configured:
  rabbitmq_management
  rabbitmq_management_agent
  rabbitmq_web_dispatch
Applying plugin configuration to rabbit@vhost05...
The following plugins have been enabled:
  rabbitmq_management
  rabbitmq_management_agent
  rabbitmq_web_dispatch

started 3 plugins.
```
然后通过浏览器访问 url: `http://ip:15672`

## 添加用户并授权
默认的guest/guest用户，
```
rabbitmqctl add_user csh csh
rabbitmqctl set_user_tags csh administrator
rabbitmqctl set_permissions -p / csh ".*" ".*" ".*"
```
## 查看用户列表

```
rabbitmqctl list_users
```

## Mac特殊情况
使用HomeBrew安装rabbitmq，会在`/usr/local/etc/`的环境变量文件`rabbitmq-env.conf`中有如下配置,使得rabbitmq只能监听`127.0.01`进来的连接。

```
NODE_IP_ADDRESS=127.0.0.1
```
注释掉这部分内容后，就可以从其他机器访问连接。
