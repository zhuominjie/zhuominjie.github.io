---
title: JAVA服务通过URL下载文件
tags:
  - Java
  - URLConnection
categories: Java
abbrlink: c0372a48
date: 2018-04-18 14:36:47
---

如何通过Java（模拟浏览器）发送HTTP请求，下载文件是WEB应用经常处理的场景。Java有原生的API可用于发送HTTP请求，即：java.net.URL、java.net.URLConnection。这些API本身已经足够好用，但不够简便。所以，也流行有许多Java HTTP请求的framework，如Apache的HttpClient等。

目前项目主要用到Java原生的方式，所以，这里主要介绍此方式。
<!--more--> 
## 1.Get请求和Post请求
HTTP请求简单分为GET请求和POST请求（详见：[Hypertext Transfer Protocol -- HTTP/1.1 - Method Definitions](https://tools.ietf.org/html/rfc2616#section-9)）。

使用Java发送这两种请求的代码大同小异，只是一些参数设置的不同。步骤如下：

1. 通过统一资源定位器（java.net.URL）获取连接器（java.net.URLConnection）
2. 设置请求的参数
3. 发送请求
4. 以输入流的形式获取返回内容
5. 关闭输入流

**HttpURLConnection**继承自**URLConnection**，常用它下面几个方法：

* -setRequestMethod：设置URL请求的方法， 如GET、POST、HEAD、OPTIONS、PUT、DELETE、TRACE。其中，GET方法是默认的，可以不写；
* -setRequestProperty：设置一般请求属性，如"Accept-Charset"、"Content-Type"、"User-Agent"等；
* -getResponseCode：从HTTP响应消息获取状态码；
* -getResponseMessage：获取与来自服务器的响应代码一起返回的HTTP响应消息（如果有）。

## 2.下载文件
文件下载请求，本质上也是一个POST请求，因此，流程大体相同。下面的示例正是按照流程顺序进行了处理。如果项目中有多处地方使用HTTP请求，我们可以适当对其进行封装。


### 封装文件下载器

```java

/**
 * 从网络Url中下载文件
 * @param urlStr
 * @param fileName
 * @param savePath
 * @throws IOException
 */
public static void  downLoadFromUrl(String urlStr,String fileName,String savePath) 
			throws IOException{
    URL url = new URL(urlStr);
    HttpURLConnection conn = (HttpURLConnection)url.openConnection();
    //设置超时间为3秒
    conn.setConnectTimeout(3*1000);
    //防止屏蔽程序抓取而返回403错误
    conn.setRequestProperty("User-Agent", "Mozilla/4.0 (compatible; MSIE 5.0; 
    				Windows NT; DigExt)");

    //得到输入流
    InputStream inputStream = conn.getInputStream();

	 //转储文件
    saveToLocal(inputStream, savePath, fileName);

    System.out.println("info:"+url+" download success");

}
```
### 执行文件转储

```java
/**
 * 文件转储
 * @param in
 * @param fileName
 * @param savePath
 * @throws IOException
 */
public static void saveToLocal(InputStream in, String fileName, String savePath) 
			throws IOException {
    //获取字节流
    byte[] getData = readInputStream(in);

    //父文件夹位置
    File saveDir = new File(savePath);
    if(!saveDir.exists()){
        saveDir.mkdir();
    }

    File file = new File(saveDir+File.separator+fileName);
    FileOutputStream fos = new FileOutputStream(file);
    fos.write(getData);

    //关闭输入输出流
    if(fos!=null){
        fos.close();
    }
    if(in!=null){
        in.close();
    }
}
```
### 输入流转字节流
```java
/**
 * 从输入流中获取字节数组
 * @param inputStream
 * @return
 * @throws IOException
 */
public static  byte[] readInputStream(InputStream inputStream) 
			throws IOException {
    byte[] buffer = new byte[1024];
    int len = 0;
    ByteArrayOutputStream bos = new ByteArrayOutputStream();
    while((len = inputStream.read(buffer)) != -1) {
        bos.write(buffer, 0, len);
    }
    bos.close();
    return bos.toByteArray();
}    
```

## 参考：
如果想学习如何用URLConnection发送Get请求和Post请求，可参考这篇好文：[通过java.net.URLConnection发送HTTP请求的方法](https://www.cnblogs.com/nick-huang/p/3859353.html)

