---
title: 'Maven:管理本地jar包'
tags:
  - Maven
keywords:
  - Maven
categories: Maven
abbrlink: 46b8b7d2
date: 2018-06-01 10:16:49
description:
---
Maven依赖管理，可以很方便的帮助我们添加所需的jar包。但是，大部分情况，这些jar包都是Maven项目。如果我们需要引入自定义的jar包，又该如何处理呢？

起初，我在项目中新建`lib`文件夹，并把自定义的jar包放进去，然后，在`pom.xml`中进行如下配置：
```
<dependency>
	<groupId>dd-plist</groupId>
	<artifactId>dd-plist</artifactId>
	<version>1.0</version>
	<scope>system</scope>
	<systemPath>${project.basedir}/lib/dd-plist.jar</systemPath>
</dependency>
```
结果，本地使用IDE运行调试时没有问题，但是打成jar包上传到服务器却找不到该jar包中的类文件。

为了解决这个问题，我找到了如下的方案。

<!--more-->

## 1 在项目的目录下创建lib文件夹
## 2 使用Maven安装自定义jar包

在命令行执行以下命令，可以安装自定义jar包到我们指定的lib文件夹。注意配置好自定义jar包的路径。

```
mvn install:install-file 
-Dfile=path_to_mylib.jar -DgroupId=com.mylib 
-DartifactId=jar_name -Dversion=1.0 -Dpackaging=jar 
-DlocalRepositoryPath=path_to_my_project/lib 
```
执行成功后，终端显示内容如下：

```
[INFO] Scanning for projects...
[INFO]                                                                         
[INFO] ------------------------------------------------------------------------
[INFO] Building Maven Stub Project (No POM) 1
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- maven-install-plugin:2.4:install-file (default-cli) @ standalone-pom ---
[INFO] Installing path_to_mylib.jar to path_to_my_project/lib/com/mylib/jar_name/1.0/dd-plist-1.0.jar
[INFO] Installing /var/folders/r5/86whqmls4y36p5qgnhwstr6w0000gn/T/mvninstall2596441059296695813.pom to path_to_my_project/lib/com/mylib/jar_name/1.0/dd-plist-1.0.pom
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 0.413 s
[INFO] Finished at: 2018-06-01T10:13:20+08:00
[INFO] Final Memory: 9M/309M
[INFO] ------------------------------------------------------------------------
```
## 3 配置POM文件
按如下文件配置POM.xml，配置完成之后，重新执行`Maven Reimport`，即可生效。
```
<repositories>
     <repository>
         <!-- DO NOT set id to "local" because it is reserved by Maven -->
         <id>lib</id>
         <url>file://${project.basedir}/lib</url>
     </repository>
  </repositories>
  <dependencies>
    <dependency>
        <groupId>com.mylib</groupId>
        <artifactId>mylib</artifactId>
        <version>1.0</version>
    </dependency>
  </dependencies>
```

## 结语
通过以上方式配置的自定义jar包，在工程打包部署后仍然可以生效。具体的原理，可以留言给我。我很乐意多学习一些Maven的知识。

