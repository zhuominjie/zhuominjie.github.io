---
title: Nginx-平滑升级
tags:
  - Nginx
  - 平滑升级
keywords:
  - Nginx
  - 平滑升级
category: Nginx
abbrlink: 6ea553c6
date: 2018-12-20 09:05:13
---
## 概述
当面临新的需求，需要安装新的模块；或者遇到安全检查，需要安装安全补丁；甚至为了更新到最新版本的Nginx，我们如何能保证现有业务不中断，平滑地升级Nginx成了很有必要的课题。

## 平滑升级原理
#### 多进程模式下的请求分配方式
Nginx默认工作在多进程模式下，即主进程（master process）启动后完成配置加载和端口绑定等动作，fork出指定数量的工作进程（worker process），这些子进程会持有监听端口的文件描述符（fd），并通过在该描述符上添加监听事件来接受连接（accept）。

#### 信号的接收和处理
Nginx主进程在启动完成后会进入等待状态，负责响应各类系统消息，如SIGCHLD、SIGHUP、SIGUSR2等。

<!--more-->
#### Nginx信号简介
主进程支持的信号

* TERM, INT: 立刻退出
* QUIT: 等待工作进程结束后再退出
* KILL: 强制终止进程
* HUP: 重新加载配置文件，使用新的配置启动工作进程，并逐步关闭旧进程。
* USR1: 重新打开日志文件
* USR2: 启动新的主进程，实现热升级
* WINCH: 逐步关闭工作进程

工作进程支持的信号

* TERM, INT: 立刻退出
* QUIT: 等待请求处理结束后再退出
* USR1: 重新打开日志文件

## 平滑升级实战
#### 备份原Nginx二进制文件
将Nginx的安装目录整体备份

```
[root@zzz ~]# cp /usr/local/nginx/ /usr/local/nginx_$(date +%F)
```
#### 编译新的nginx源码包
编译新Nginx源码，安装路径需与旧版一致

```
[root@zzz nginx-1.15.7]# ./configure --prefix=/usr/local/nginx
[root@zzz nginx-1.15.7]# make&make install
```

#### 发送USR2信号
向主进程发送USR2信号，Nginx会启动一个新版本的master进程和工作进程，和旧版一起处理请求

```
[root@zzz ~]# ps -ef|grep nginx
##打印内容
root      3669     1  0 12月19 ?      00:00:00 nginx: master process ./sbin/nginx -c ./conf/nginx.conf
root      3670  3669  0 12月19 ?      00:00:01 nginx: worker process
root      3671  3669  0 12月19 ?      00:00:00 nginx: worker process
root      3672  3669  0 12月19 ?      00:00:01 nginx: worker process
root      3673  3669  0 12月19 ?      00:00:02 nginx: worker process

[root@zzz ~]# kill -USR2 3669
```

#### 发送WITCH信号
向原Nginx主进程发送WINCH信号，它会逐步关闭旗下的工作进程（主进程不退出），这时所有请求都会由新版Nginx处理

```
[root@zzz ~]# kill -WITCH 3669
```

但是，我尝试关闭原来的Nginx进程却无法关闭，于是，用了`QUIT` 命令

```
[root@zzz ~]# kill -QUIT 3669
```

#### 发送HUP信号
如果这时需要回退，可向原Nginx主进程发送HUP信号，它会重新启动工作进程， 仍使用旧版配置文件 。然后可以将新版Nginx进程杀死（使用QUIT、TERM、或者KILL）

```
[root@zzz ~]# kill -HUP 3669
```
注：此步骤只需在回滚的时候执行即可

#### 升级完毕
如果不需要回滚，可以将原Nginx主进程杀死（使用QUIT、TERM、或者KILL），至此完成热升级。


