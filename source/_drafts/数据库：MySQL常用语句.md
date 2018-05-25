---
title: 数据库：MySQL常用语句
abbrlink: a81bce10
tags:
---

## 导出表结构和数据

使用`mysqldump`命令，具体用法如下：

```
mysqldump -u用戶名 -p密码 -d 数据库名 表名 > 脚本名;
```
导出整个数据库结构和数据

```
mysqldump -h localhost -uroot -p123456 database > dump.sql
```

导出单个数据表结构和数据

```
mysqldump -h localhost -uroot -p123456  database table > dump.sql
```

导出整个数据库结构（不包含数据）

```
mysqldump -h localhost -uroot -p123456  -d database > dump.sql
```

导出单个数据表结构（不包含数据）

```
mysqldump -h localhost -uroot -p123456  -d database table > dump.sql
```

## 异常
可能在执行导出命令时，会遇到如下异常：

```
mysqldump: Error: 'Got error 28 from storage engine' when trying to dump tablespaces
mysqldump: Couldn't execute 'show fields from `consumption`': Got error 28 from storage engine (1030)
```

**原因** 是系统空间不足，可以清理一下缓存。
