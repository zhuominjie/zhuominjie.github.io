---
title: Quartz：Listener注入Spring对象
tags:
  - Quartz
keywords:
  - Quartz
categories: Java
abbrlink: 1b401ae4
date: 2018-06-22 15:24:46
---
我们知道，由于`listener`启动会先于`spring`容器。况且`quratz` 中的`bean`是独立于`spring`外运行的，也就是说`spring`把创建`bean`的生命周期权限托管给了`quratz`所以要想在`job` 或者`job`的`listener`中注入`spring`的`bean`肯定是不行的 但是我们可以使用别的方法。下面我写一个自己认为最简单的方法：

先创建一个`spring`的`SpringUtil`的工具类，该工具类主要是利用实现了`ApplicationContextAware`接口，利用`set`方法先实例化一个`ApplicationContext`，然后就可以获取对应的bean源码如下:

```java
import org.springframework.beans.BeansException;
import org.springframework.context.ApplicationContext;
import org.springframework.context.ApplicationContextAware;
import org.springframework.stereotype.Component;

@Component
public class SpringUtil implements ApplicationContextAware{

    private static ApplicationContext applicationContext;

    @Override
    public  void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        if(SpringUtil.applicationContext == null) {
            SpringUtil.applicationContext = applicationContext;
        }
    }

    //获取applicationContext
    public static ApplicationContext getApplicationContext() {
        return applicationContext;
    }

    //通过name获取 Bean.
    public static Object getBean(String name){
        return getApplicationContext().getBean(name);
    }

    //通过class获取Bean.
    public static <T> T getBean(Class<T> clazz){
        return getApplicationContext().getBean(clazz);
    }

    //通过name,以及Clazz返回指定的Bean
    public static <T> T getBean(String name,Class<T> clazz){
        return getApplicationContext().getBean(name, clazz);
    }

}
```

使用方法

```java
@Component
public class MySchedulerListener implements SchedulerListener{
    ...
    @Override
    public void jobScheduled(Trigger trigger) {
        MyBatisMapper myBatisMapper = (MyBatisMapper)SpringUtil.getBean(MyBatisMapper.class);
        myBatisMapper.updateByPrimaryKey(0);
        System.out.println("任务被部署时被执行");
    }
    ...
}
```