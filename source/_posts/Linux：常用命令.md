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




