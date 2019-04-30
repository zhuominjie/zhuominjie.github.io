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

## 配置策略

#### 1. 负载均衡同源策略

当同时使用负载均衡和CAS单点登录的时候，upstream的多个后端会分别于CAS Server建立会话，导致登录流程异常。该问题可以通过配置同源策略，根据访客ip固定被调用服务器。

```
http {
	upstream gateway {
        ip_hash;
        server 172.28.20.114:8080 ;
        server 172.28.20.140:8080 ;
        server 172.28.20.122:8080 ;

        }
}

```

## 常见问题

#### 1. 缺少`C compiler`库

```
./configure: error: C compiler cc is not found
```

安装

```
yum -y install gcc gcc-c++
```

#### 2. 缺少`PCRE`库

```
./configure: error: the HTTP rewrite module requires the PCRE library.
```
使用`yum`安装

```
yum -y install pcre-devel
```

#### 3. 缺少`OpenSSL`库

安装`--with-http_ssl_module`模块，缺少依赖

```
./configure: error: SSL modules require the OpenSSL library.
```
##### `Linux`环境下安装

```
yum -y install openssl openssl-devel
```

##### `Mac`环境下
由于不能安装`openssl`，因此，需要到官网下载openssl 压缩包。

地址： https://www.openssl.org/source/

安装命令修改为：

```
/configure --prefix=/usr/local/nginx --with-http_ssl_module --with-openssl=<path>
```
<path> 是openssl文件解压位置。

但是，在`make`编译过程中，可能存在如下问题：

```
ld: symbol(s) not found for architecture x86_64
clang: error: linker command failed with exit code 1 (use -v to see invocation)
```

解决方法

```
#在源码目录下
vi objs/Makefile

#找到类似下面这行
&& ./config --prefix=/Users/wid/Downloads/nginx-1.8.0/../openssl-1.0.2d/.openssl no-shared
```
将`config`修改为`Configure darwin64-x86_64-cc`, `--prefix`之后的不用修改, 修改后的如:

```
&& ./Configure darwin64-x86_64-cc --prefix=/Users/wid/Downloads/nginx-1.8.0/../openssl-1.0.2d/.openssl no-shared
```

保存后，继续`make`即可
