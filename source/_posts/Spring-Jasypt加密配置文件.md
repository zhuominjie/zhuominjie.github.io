---
title: Spring-Jasypt加密配置文件
tags:
  - Spring
  - Jasypt
  - DES
keywords:
  - Spring
  - Jasypt
  - DES
categories: Spring
abbrlink: d39f004c
date: 2018-10-24 09:38:31
---
在开发WEB项目的时候，会使用到很多中间件，比如数据库、redis、消息队列等等，或者涉及敏感信息，如邮箱等账号信息。如果采用明文方式保存在配置文件或者代码中，会产生很大风险。因此，如何加密这些信息将是一个很有用的话题。

本文采用`Jasypt`对基于`Spring Boot`的项目进行加密。`Jasypt`默认使用DES对文本信息进行加密，通过适当的配置，可以重载配置文件的载入过程，实现自动解密的功能。

## 引入 Maven 依赖

```
<dependency>
  <groupId>com.github.ulisesbocchio</groupId>
  <artifactId>jasypt-spring-boot</artifactId>
  <version>2.1.0</version>
</dependency>
```

<!--more-->
## 定义加密盐
在`application.properties`配置文件中增加如下内容（加解密时使用）

```
jasypt.encryptor.password=secret!@#$
```
## 生成密钥
可以在main函数或者测试类中，使用`StringEncryptor`生成密钥。

```Java
@RunWith(SpringRunner.class)
@SpringBootTest
@WebAppConfiguration
public class EncryptorTest {
    @Autowired
    StringEncryptor encryptor;

    @Test
    public void getPass() {
        System.out.print("Password\n");
        String p = encryptor.encrypt("Password");
        System.out.print(p+"\n");
    }
}
```

## 替换原来的文本

用上面生成的加密后的字符，替换配置文件中对应的密码，替换后如下：

```
#spring.datasource.password=Password
spring.datasource.password=ENC(UBtTQGk1xbdmhff+UUoT6g==)  
```

只有符合格式才能自动处理，注意格式为：ENC(密钥)

## FQ
如果在调用`StringEncryptor`类的时候，警告不存在该类的话，可以在入口函数增加`@EnableEncryptableProperties`注释

```Java
@SpringBootApplication
@EnableEncryptableProperties
public class MyWebApplication {
    public static void main(String[] args) {
        SpringApplication.run(MyWebApplication.class, args);
    }
}
```

## 参考
[Jasypt](https://github.com/ulisesbocchio/jasypt-spring-boot)项目的 GitHub 地址
