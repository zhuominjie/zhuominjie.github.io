---
title: Spring-Application.properties用法
tags:
  - Spring
  - Java
keywords:
  - Spring
  - Java
categories: Spring
abbrlink: 3a9e5616
date: 2018-10-22 10:30:44
description:
---

### 参数间引用 
在`application.properties`中的各个参数之间也可以直接引用来使用，就像下面的设置：

```
com.name="张三"
com.id="8"
com.psrInfo=${com.name}编号为${com.id}
```
这样我们就可以只是用psrInfo这个属性就好
