---
title: Appium-环境部署
abbrlink: 42fc8a9
date: 2018-10-16 14:54:54
tags:
keywords:
description:
---
# 安装Java jdk1.8
#### 搭建环境：
Ubuntu 16.04 x64
JDK :jdk-8u171-linux-x64.tar.gz

#### 下载linux对应的安装包
下载地址：http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html

#### 解压安装包jdk-8u171-linux-x64.tar.gz

```
tar -zxvf jdk-8u171-linux-x64.tar.gz
```
<!--more-->
#### 转移目录到 /usr/lib 目录下

```
cd  /usr/lib
sudo mkdir jdk
sudo mv ~/jdk1.8.0_171/ usr/lib/jdk
```

#### 配置java环境变量

```
sudo vi /etc/bash.bashrc
```
在末尾加以下几行文字：

```
#set java env
export JAVA_HOME=/usr/lib/jdk/jdk1.8.0_171
export JRE_HOME=${JAVA_HOME}/jre    
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib    
export PATH=${JAVA_HOME}/bin:$PATH 
```
执行命令使修改立即生效

```
source /etc/profile 
```

#### 测试安装是否成功
在终端输入，出现版本号则表示安装成功

```
java -version
```

# 安装 Android SDK
#### 依赖安装
Android SDK中的adb程序是32位的，Ubuntu x64系统需要安装32位库文件，用于兼容32位的程序。如果不安装，adb会出错：java.io.IOException: error=2 
输入下面命令，安装库文件（国内需先换源）

```
sudo apt-get install -y libc6-i386 lib32stdc++6 lib32gcc1 lib32ncurses5 lib32z1 
```
#### 下载 SDK Tools

地址：http://www.androiddevtools.cn/ 
在`AndroidDevTools`中下载`SDK Tools`，在`Downloads`文件夹下找到下载的文件，并将`android-linux-sdk`移动到`/opt/`目录下：

```
sudo mv /Download/android-sdk-linux /opt/
```
#### 配置Android SDK 环境变量

```
sudo vi /etc/bash.bashrc
```
在末尾增加如下内容：

```
export ANDROID_SDK_HOME=/opt/android-sdk-linux
export PATH=$PATH:${ANDROID_SDK_HOME}/tools
export PATH=$PATH:${ANDROID_SDK_HOME}/platform-tools
```

# 踩坑
1. Appium执行过程中，执行adb shell 命令，导致appium测试中断：
原因：linux使用yum和直接拷贝Android SDK的方式，在使用adb的时候会导致appium和系统adb分别使用两个不同文件的情况，导致端口占用。删掉一个可以解决。
