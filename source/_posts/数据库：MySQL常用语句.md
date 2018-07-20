---
title: 数据库：MySQL常用语句
tags:
  - MySQL
keywords:
  - MySQL
categories: 数据库
abbrlink: a81bce10
date: 2018-06-21 09:57:46
---

## 查询字段为null或者不为null
在`mysql`中，查询某字段为空时，切记不可用`= null`，而是 `is null`，不为空则是`is not null`，示例如下：

```
select * from table where column is null;
select * from table where column is not null;
```

## group by 异常
MySQL5.7 后将`sql_mode`的`ONLY_FULL_GROUP_BY`模式默认设置为打开状态，这样一来，很多之前的sql语句可能会出现错误，错误信息如下：

```
Error Code: 1055. Expression #3 of SELECT list is not in GROUP BY clause and contains nonaggregated column '×××' which is not functionally dependent on columns in GROUP BY clause; this is incompatible with sql_mode=only_full_group_by
```
解决方法：

1、查看sql_mode

```
select @@sql_mode
```
查询出来的值为：

```
ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
```
2、去掉ONLY_FULL_GROUP_BY，重新设置值

```
set @@sql_mode ='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER
,NO_ENGINE_SUBSTITUTION';
```

3、上面是改变了全局sql_mode，对于新建的数据库有效。对于已存在的数据库，则需要在对应的数据下执行

```
set sql_mode ='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';
```

4、以上的改表方式只能临时生效，在MySQL重启之后就失效了，想要一劳永逸的办法，就是修改配置文件。RPM的安装方式，配置文件位于`/etc/my.cnf`，增加如下配置：

```
sql_mode=STRICT_TRANS_TABLES,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
```

## 忽略表名大小写
在配置文件`my.cnf`在增加如下配置：

```
#默认是0，区分大小写；1忽略大小写
lower_case_table_names=1
```
如果原有的数据库表中有大写的表名，在配置生效后，会出现该表查找不到的异常。在配置之前，将含有大写的表导出，在配置生效之后，重新导入，可以消除异常。

