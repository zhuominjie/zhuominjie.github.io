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
