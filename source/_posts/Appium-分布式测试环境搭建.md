---
title: 'Appium:分布式测试环境搭建'
tags:
  - Appium
  - Grid
  - Selenium
categories: Appium
keywords:
  - Appium
  - Grid
  - Selenium
abbrlink: '4e971816'
date: 2018-07-30 11:01:10
---

## 需求
同时向大量的跨平台机器推送脚本，执行测试。

## 环境搭建
### 1.Selenium Standalone Server
1、下载`jar`包，地址为：`https://www.seleniumhq.org/download/`

2、启动服务

```
java -jar selenium-server-standalone-<version>.jar -role hub
```
服务启动后，访问：`localhost：4444`验证。

3、编辑节点配置文件

```
{
  "capabilities":
      [
        {
          "browserName": "<e.g._iPhone5_or_iPad4>",
          "version":"<version_of_iOS_e.g._7.1>",
          "maxInstances": 1,
          "platform":"<platform_e.g._MAC_or_ANDROID>"
        }
      ],
  "configuration":
  {
    "cleanUpCycle":2000,
    "timeout":30000,
    "proxy": "org.openqa.grid.selenium.proxy.DefaultRemoteProxy",
    "url":"http://<host_name_appium_server_or_ip-address_appium_server>:<appium_port>/wd/hub",
    "host": <host_name_appium_server_or_ip-address_appium_server>,
    "port": <appium_port>,
    "maxSession": 1,
    "register": true,
    "registerCycle": 5000,
    "hubPort": <grid_port>,
    "hubHost": "<Grid_host_name_or_grid_ip-address>"
  }
}
```

<!--more-->
如果没有给出 url、host 和 port，配置会自动指向 localhost:whatever-port-Appium-started-on。

如果你的 Appium Server 和 Selenium Grid 没有运行在同一台机器上，为确保 Selenium Grid 连接正常，请在你的 host & url 上使用外部域名或 IP 地址，而不是 localhost 和 127.0.0.1

4、指定配置文件启动Appuium节点
```
appium -p 4724 -bp 4714 --nodeconfig "json文件路径"
```
其中，`-p`与配置文件中的`port`一致，即指定`appium`启动的端口号，`-bp`是手机客户端`bootStrap`监听的端口号

启动后，控制台输出如下：

```
[Appium] Appium support for versions of node < 8 has been deprecated and will be removed in a future version. Please upgrade!
[Appium] Welcome to Appium v1.8.1
[Appium] Non-default server args:
[Appium]   port: 4724
[Appium]   bootstrapPort: 4714
[Appium]   nodeconfig: SM-G9550
[debug] [Appium] Starting auto register thread for grid. Will try to register every 5000 ms.
[Appium] Appium REST http interface listener started on 0.0.0.0:4724
[debug] [Appium] Appium successfully registered with the grid on http://localhost:4444
```

## 问题
Selenium Grid会发生阻塞，如果上一个测试任务因为异常无法继续执行会发生阻塞，后面的测试任务无法继续执行

## Selenium Grid 架构
![selenium-grid架构图.png](https://i.loli.net/2019/04/30/5cc7ba0f46e13.png)

什么时 Hub呢？

它是你加载所有测试的机器。

一个Grid里只有一个Hub。

hub是测试运行的地方，但是你看到的测试是在node上的浏览器上执行的。

什么是Nodes？

这台机器将执行您在hub上加载的测试

他可以是一个或者多个nodes在一个grid里，Grid中有不同浏览器和操作系统。

#### 做成多线程方式
Selenium Grid只是提供多系统、多浏览器的执行环境，而不是说任务一个test case丢给它就能并行运行。并行的运行我这里就交给testng了

