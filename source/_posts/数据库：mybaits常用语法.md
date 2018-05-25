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