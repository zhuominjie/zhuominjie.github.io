---
title: 数据库：MyBatis常用语法
tags:
  - Linux
  - scp
keywords:
  - Linux
  - scp
categories: Linux
abbrlink: 5a838215
date: 2018-05-11 14:57:46
---

## Else判断分支

使用MyBatis写动态SQL查询相比Hiberntate是非常方便的。select不仅能够根据mapper接口中的返回值自动匹配

查询selectOne还是selectList，而且在查询中还可以灵活的定制查询的方式，添加if 或者 choose等标签进行查询。mybaits中没有else要用“chose when otherwise”代替


```xml
<!--批量插入用户-->
<select id="getItems" parameterType="com.ipro.shopping.to.IntegerEntity" resultType="itemsType">  
   select * from itemsType  
   <where>  
      <!--方式一使用choose的方式查询-->  
     <!-- <choose>  
         <when test="parentId !=0 ">parentTypeId=#{parentId}</when>  
         <when test="parentId==0">parentTypeId is null</when>  
      </choose> -->  
     <!--方式二使用if的方式查询-->  
      <if test="parentId!=0">  
         parentTypeId=#{parentId}  
      </if>  
      <if test="parentId==0">  
        parentTypeId is null  
      </if>  
   </where>  
</select>
```

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

### 可能异常
可能在执行导出命令时，会遇到如下异常：

```
mysqldump: Error: 'Got error 28 from storage engine' when trying to dump tablespaces
mysqldump: Couldn't execute 'show fields from `consumption`': Got error 28 from storage engine (1030)
```

**原因** 是系统空间不足，可以清理一下缓存。

## 数据库外键关联
外键关联有四种模式，分别如下：

**CASCADE:** 从父表中删除或更新对应的行，同时自动的删除或更新子表中匹配的行。`ON DELETE CANSCADE`和`ON UPDATE CANSCADE`都被`InnoDB`所支持。

**SET NULL:** 从父表中删除或更新对应的行，同时将子表中的外键列设为空。注意，这些在外键列没有被设为`NOT NULL`时才有效。`ON DELETE SET NULL`和`ON UPDATE SET SET NULL`都被`InnoDB`所支持。

**NO ACTION:** `InnoDB`拒绝删除或者更新父表。

**RESTRICT:** 拒绝删除或者更新父表。指定`RESTRICT`（或者`NO ACTION`）和忽略`ON DELETE`或者`ON UPDATE`选项的效果是一样的

## 按天分组查询
| username   | create_time  |  
| --------   | :-----:  | 
| 张三        | 2018-03-13 09:48:34      | 
| 李四        | 2018-03-14 09:24:32      | 

查询某天：

```mysql
SELECT DATE_FORMAT( create_time, "%Y-%m-%d) as date, COUNT( * ) as count
FROM test
GROUP BY DATE_FORMAT( create_time, "%Y-%m-%d) 
```
结果：

| date   | count  |  
| --------   | :-----:  | 
| 2018-03-13 |  1      | 
| 2018-03-14 |  1      | 

如果想要查询某时的统计信息，可以修改`DATE_FORMAT`内容：

`
create_time, "%Y-%m-%d %H"
`

依次类推，其实就是对`create_time`进行处理，然后再对处理后的数据分组。

## 模糊匹配
```
like CONCAT('%', #{port}, '%')
```