---
title: Liunx：常用命令
tags:
  - Linux
  - scp
keywords:
  - Linux
  - scp
categories: Linux
abbrlink: cbdebe31
date: 2018-05-11 14:57:46
---

## SCP-文件传输
### 从服务器下载文件

```
scp username@servername:/remote_path/filename ~/local_destination
```

### 从服务器下载整个目录

```
scp -r username@servername:/remote_path/remote_dir/ ~/local_destination
```
### 上传本地文件到服务器

```
scp ~/local_path/local_filename username@servername:/remote_path
```

### 上传目录到服务器
```
scp  -r ~/local_dir username@servername:/remote_path/remote_dir
```
<!--more-->

## 查看内存
### free命令 

```
              total        used        free      shared  buff/cache   available
Mem:        8010180     1027572      454540       74152     6528068     6504456
Swap:             0           0           0
```

**total:**总计物理内存的大小。
**used:**已使用多大。
**free:**可用有多少。
**Shared:**多个进程共享的内存总额。
**Buffers/cached:**磁盘缓存的大小。 

`Mem`的`used/free`是从`OS`的角度来看，因为对于`OS`，`buffers/cached`都是属于被使用的

## 修改密码
1. 登录账户后，输入`passwd`
2. 输入一遍旧密码，输入两遍新密码，密码不能太简单

## 软连接
通常数据文件会和应用部署所在系统盘分开。此时，为了既能保持整体一致性，又不会占用系统盘资源，通常会在应用部署的文件夹下建立一个指向数据盘的软连接。

**命令：**ln -s abc(源文件) efg(目标快捷方式)

```
ln -s /data/upload/ ./upload
```
**效果：**当前目录产生如下文件目录

```
lrwxrwxrwx 1 root root       13 6月  20 15:35 upload -> /data/upload/
```

注意：必须使用绝对路径，否则会出现`符号连接的层数过多`的错误。
