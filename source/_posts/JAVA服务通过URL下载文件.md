---
title: JAVA服务通过URL下载文件
date: 2018-04-18 14:36:47
tags: [Java, URLConnection]
categories: Java
---

## 概述
如何通过Java发送HTTP请求，通俗点讲，如何通过Java（模拟浏览器）发送HTTP请求。

Java有原生的API可用于发送HTTP请求，即java.net.URL、java.net.URLConnection，这些API很好用、很常用，但不够简便；

所以，也流行有许多Java HTTP请求的framework，如，Apache的HttpClient。

目前项目主要用到Java原生的方式，所以，这里主要介绍此方式。

<!--more--> 

## 下载文件

```java
/**
 * 从网络Url中下载文件
 * @param urlStr
 * @param fileName
 * @param savePath
 * @throws IOException
 */
public static void  downLoadFromUrl(String urlStr,String fileName,String savePath) throws IOException{
    URL url = new URL(urlStr);
    HttpURLConnection conn = (HttpURLConnection)url.openConnection();
    //设置超时间为3秒
    conn.setConnectTimeout(3*1000);
    //防止屏蔽程序抓取而返回403错误
    conn.setRequestProperty("User-Agent", "Mozilla/4.0 (compatible; MSIE 5.0; Windows NT; DigExt)");

    //得到输入流
    InputStream inputStream = conn.getInputStream();
    //获取自己数组
    byte[] getData = readInputStream(inputStream);

    //文件保存位置
    File saveDir = new File(savePath);
    if(!saveDir.exists()){
        saveDir.mkdir();
    }
    File file = new File(saveDir+File.separator+fileName);
    FileOutputStream fos = new FileOutputStream(file);
    fos.write(getData);
    if(fos!=null){
        fos.close();
    }
    if(inputStream!=null){
        inputStream.close();
    }


    System.out.println("info:"+url+" download success");

}
```

```java
/**
 * 从输入流中获取字节数组
 * @param inputStream
 * @return
 * @throws IOException
 */
public static  byte[] readInputStream(InputStream inputStream) throws IOException {
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


## 测试
```java
public static void main(String[] args) {
    try{
        downLoadFromUrl("http://172.18.33.247:8081/files/5ad6a162b1727a35cc7dff9b",
                "test.png","/Users/zhuominjie/Desktop/1工作/4归一化平台/");
    }catch (Exception e) {
        // TODO: handle exception
    }
}
```

## 参考
JAVA原生API实现GET和POST请求可参考：[通过java.net.URLConnection发送HTTP请求的方法](https://www.cnblogs.com/nick-huang/p/3859353.html)

