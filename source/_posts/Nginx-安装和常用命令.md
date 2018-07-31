---
title: 'Nginx:安装和常用命令'
categories: Nginx
tags:
  - Nginx
keywords:
  - Nginx
abbrlink: 8747ea0e
date: 2018-07-30 19:23:59
---
## 下载安装包
从官网下载`nginx`压缩包，官网地址：`https://nginx.org/en/download.html`。最好选择稳定版本。

## 安装
假设下载的安装包位于`/root/`用户根目录下。

1、解压文件
```
tar -zxvf nginx-1.15.2.tar.gz 
```
2、自定义nginx安装位置

```
#创建文件夹
cd /usr/local
mkdir nginx-1.15.2

#回到解压文件夹
cd /root/nginx-1.15.2
指定安装路径
./configure --prefix=/usr/local/nginx-1.15.2
编译安装
make
make install
```

## 常用命令
先进入到nginx的安装路径，可以看到目录结构如下：

```
drwx------ 2 root root    6 7月  30 19:05 client_body_temp
drwxr-xr-x 2 root root 4096 7月  30 19:16 conf
drwx------ 2 root root    6 7月  30 19:05 fastcgi_temp
drwxr-xr-x 5 root root  141 7月  30 19:15 html
drwxr-xr-x 2 root root   58 7月  30 19:12 logs
drwx------ 2 root root    6 7月  30 19:05 proxy_temp
drwxr-xr-x 2 root root   19 7月  30 19:04 sbin
drwx------ 2 root root    6 7月  30 19:05 scgi_temp
drwx------ 2 root root    6 7月  30 19:05 uwsgi_temp
```
其中，conf是配置文件，html是静态文件目录，logs是错误日志目录，sbin是执行文件目录

1、启动

```
./sbin/nginx
```

2、停止

```
./sbin/nginx -s stop
```

3、重载配置文件

```
./sbin/nignx -s reload
```

4、指定配置文件启动

```
./sbin/nginx -c /conf/nginx.conf
```