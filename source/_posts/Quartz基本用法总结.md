---
title: Quartz基本用法总结
date: 2018-04-28 16:13:42
tags: [Java, Spring]
categories: Java
---

OpenSymphony所提供的Quartz是任务调度领域享誉盛名的开源框架。Spring提供了集成Quartz的功能，可以让开发人员以更面向Spring的方式创建基于Quartz的任务调度应用。任务调度本身设计多线程并发、运行时间规则制定及解析、运行现场保持与恢复、线程池维护等诸多方面的工作。如果以自定义线程池的原始方法开发，难点很大。
<!--more-->
## 普通JAVA任务
启动基本的Quartz任务包含一下流程：

1. 创建任务类，实现Job接口的void execute(JobExecutionContext context)方法，定义被执行任务的执行逻辑；
2. 获取JobDetail对象，
2. 获取Scheduler对象。

```java
import org.quartz.*;
import org.quartz.impl.StdSchedulerFactory;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import java.util.Date;

public class RAMQuartz {
    private static Logger logger = LoggerFactory.getLogger(RAMQuartz.class);

    public static void main(String[] args) throws SchedulerException {
    	//创建scheduler
        SchedulerFactory sf = new StdSchedulerFactory();
        Scheduler scheduler = sf.getScheduler();

	//定义一个JobDetail
	//定义Job类为RAMJob类，这是真正的执行逻辑所在
        JobDetail jb = JobBuilder.newJob(RAMJob1.class) 
                .withDescription("this is a ram job")
                .withIdentity("ramJob", "ramGroup")//定义name/group
                .build();
	//通过JobDataMap传递参数
        jb.getJobDataMap().put("Test", "This is test parameter value");

        long time = System.currentTimeMillis() + 3*1000L;
        Date startTime = new Date(time);

	//定义一个Trigger
        Trigger trigger = TriggerBuilder.newTrigger()
                .withDescription("")
                .withIdentity("ramTrigger", "ramTriggerGroup")//定义name/group
                .startAt(startTime)//加入scheduler后，在指定时间启动
                //使用CronTrigger
                .withSchedule(CronScheduleBuilder.cronSchedule("0/2 * * * * ?"))
                .build();
	//绑定任务和定时器到调度器
        scheduler.scheduleJob(jb,trigger);

	//启动
        scheduler.start();
        logger.info("启动时间 ： " + new Date());
    }
}
```

```java
import org.quartz.Job;
import org.quartz.JobDataMap;
import org.quartz.JobExecutionContext;
import org.quartz.JobExecutionException;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;

import java.util.Date;

public class RAMJob1 implements Job{

    private static Logger logger = LoggerFactory.getLogger(RAMJob.class);

    @Override
    public void execute(JobExecutionContext jobExecutionContext) 
    				throws JobExecutionException {

        try {
            JobDataMap dataMap = jobExecutionContext.getJobDetail().getJobDataMap();
            String str = dataMap.getString("Test");
            logger.info("Quartz dataMap : " + new Date() + "\n" + str);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

## 对象注入