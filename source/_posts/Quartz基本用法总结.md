---
title: Quartz系列一：基本用法总结
tags:
  - Java
  - Spring
  - Quartz
  - 持久化
  - 对象注入
categories: Java
abbrlink: f6df2318
date: 2018-04-28 16:13:42
---

OpenSymphony所提供的Quartz是任务调度领域享誉盛名的开源框架。Spring提供了集成Quartz的功能，可以让开发人员以更面向Spring的方式创建基于Quartz的任务调度应用。任务调度本身设计多线程并发、运行时间规则制定及解析、运行现场保持与恢复、线程池维护等诸多方面的工作。如果以自定义线程池的原始方法开发，难点很大。
<!--more-->
## 1.普通JAVA任务
启动基本的Quartz任务包含一下流程：

1. 创建任务类：实现Job接口的void execute(JobExecutionContext context)方法，定义被执行任务的执行逻辑；
2. 生成JobDetail对象：通过加载任务类（不是实例）来绑定任务逻辑与任务信息；
3. 生成Trigger对象：定时器的触发时间有两种方式可以定义，分别是CronSchedule和simpleSchedule()。前者使用正则表达式，后者则是简单封装后的定时器。
4. 获取Scheduler对象：通过`StdSchedulerFactory`工厂方法初始化scheduler对象，把任务和定时器绑定在一起，并启动任务。

### 完整实例代码

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

## 2.对象注入
在Spring的WEB应用中使用定时器，通常都会用到spring的特性——对象注入。前面的代码虽然能够很好地执行简单的定时器任务，但是遇到复杂的执行逻辑（如数据库读写等），就不能应付了。

下面代码可以看出，任务2需要执行myBatis的数据库插入语句：

```java
public class RAMJob2 implements Job{

    @Autowired
    private TestQuartzMapper testQuartzMapper;

    private static Logger logger = LoggerFactory.getLogger(RAMJob.class);

    @Override
    public void execute(JobExecutionContext jobExecutionContext)
    			 throws JobExecutionException {

        try {
            testQuartzMapper.insertSelective(testQuartz);
            logger.info("Insert MyBatis Success!");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```
执行这个业务逻辑，就不得不注入对象。如果仍然延用上面的方法，我们会发现执行的时候，testQuartzMapper的对象为null，结果自然毫无悬念地不断报错。

如何为我们的定时器注入Spring的对象，下面介绍一下思路：

1. 自定义`JobFactory`工厂方法，扩展`AdaptableJobFactory`，重写其`createJobInstance`方法；
2. 声明`SchedulerFactoryBean`，传入自定义的`JobFactory`工厂方法；
3. 通过新的`SchedulerFactoryBean`获取`scheduler`实例，用注入的方式在需要的地方使用。

### 完整示例

```java
import org.quartz.spi.TriggerFiredBundle;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.config.AutowireCapableBeanFactory;
import org.springframework.scheduling.quartz.AdaptableJobFactory;
import org.springframework.stereotype.Component;

@Component
public class MyJobFactory extends AdaptableJobFactory {

    @Autowired
    private AutowireCapableBeanFactory capableBeanFactory;

    @Override
    protected Object createJobInstance(TriggerFiredBundle bundle) throws Exception {
        // 调用父类的方法
        Object jobInstance = super.createJobInstance(bundle);
        // 进行注入
        capableBeanFactory.autowireBean(jobInstance);
        return jobInstance;
    }
}
```

```java
@Configuration
public class QuartzConfig {

    @Autowired
    private MyJobFactory myJobFactory;

    @Bean
    public SchedulerFactoryBean schedulerFactoryBean() throws IOException {
        SchedulerFactoryBean factory = new SchedulerFactoryBean();

        // 加载quartz数据源配置
        factory.setQuartzProperties(quartzProperties());

        // 自定义Job Factory，用于Spring注入
        factory.setJobFactory(myJobFactory);

        return factory;
    }

    @Bean
    public Scheduler scheduler() throws IOException, SchedulerException {
        Scheduler scheduler = schedulerFactoryBean().getScheduler();
        scheduler.start();
        return scheduler;
    }
}
```

### 3.Spring简单任务
Spring对Quartz进行了封装，方便开发者调用。下面以Spring Boot为例，介绍一下简单任务在Spring的执行方式。
#### 任务类定义
仔细观察可以发现，与普通Java任务的区别在于使用了@Component和@EnableScheduling的注释，相应的，就不用声明`implements Job`，以及重写`execute`方法。这是Spring提供的一种便利。

```java
@Component
@EnableScheduling
public class SpringJob {
    @Autowired
    WriteService writeService;

    private Logger logger = LoggerFactory.getLogger(this.getClass());

    public void myJobBusinessMethod() {
        this.logger.info("MyFirstExerciseJob哇被触发了哈哈哈哈哈");
        writeService.writeMSG("张三");
    }
}
```
## 配置JobDetail和Trigger的Bean
`MethodInvokingJobDetailFactoryBean`是Spring提供的JobDetail工厂方法，使用它可以快速地定义JobDetail。然而，缺点是生成的任务无法持久化保存，也就是说，无法管理任务的启动、暂停、恢复、停止等操作。
`CronTriggerFactoryBean`为表达式型触发器。

```java
@Configuration
public class QuartzJobConfig {

    /**
     * 方法调用任务明细工厂Bean
     */
    @Bean(name = "SpringJobBean")
    public MethodInvokingJobDetailFactoryBean myFirstExerciseJobBean(SpringJob springJob) {
        MethodInvokingJobDetailFactoryBean jobDetail = new MethodInvokingJobDetailFactoryBean();
        jobDetail.setConcurrent(false); // 是否并发
        jobDetail.setName("general-springJob"); // 任务的名字
        jobDetail.setGroup("general"); // 任务的分组
        jobDetail.setTargetObject(springJob); // 被执行的对象
        jobDetail.setTargetMethod("myJobBusinessMethod"); // 被执行的方法
        return jobDetail;
    }
    

    /**
     * 表达式触发器工厂Bean
     */
    @Bean(name = "SpringJobTrigger")
    public CronTriggerFactoryBean myFirstExerciseJobTrigger(@Qualifier("SpringJobBean") MethodInvokingJobDetailFactoryBean springJobBean) {
        CronTriggerFactoryBean tigger = new CronTriggerFactoryBean();
        tigger.setJobDetail(springJobBean.getObject());
        tigger.setCronExpression("0/10 * * * * ?"); // 什么是否触发，Spring Scheduler Cron表达式
        tigger.setName("general-springJobTrigger");
        return tigger;
    }
}
```
### 调度器
下面将任务和触发器注册到调度器

```java
@Configuration
public class QuartzConfig {

    /**
     * 调度器工厂Bean
     */
    @Bean(name = "schedulerFactory")
    public SchedulerFactoryBean schedulerFactory(@Qualifier("SpringJobTrigger") Trigger springJobTrigger) {
        SchedulerFactoryBean bean = new SchedulerFactoryBean();
        // 覆盖已存在的任务
        bean.setOverwriteExistingJobs(true);
        // 延时启动定时任务，避免系统未完全启动却开始执行定时任务的情况
        bean.setStartupDelay(15);
        // 注册触发器
        bean.setTriggers(SpringJobTrigger);
        return bean;
    }
}
```

完成上述配置后，启动spring boot就可以出发定时器任务了。而且，仔细观察上面的代码，在执行过程中有`WriteService`的spring对象注入，而无需我们自己去自定义JobFactory的Spring对象。

## 4.持久化
任务持久化需要用到数据库，而初始化数据库的SQL可以从下载的发布版的文件中找到，比如，我在官网的Download页下载了当前版本的Full Distribution：`Quartz 2.2.3 .tar.gz`，解压后在`quartz-2.2.3\docs\dbTables`能找到初始化脚本，因我用的是MySQL的Innodb引擎，所以我用此脚本`tables_mysql_innodb.sql`。

### 配置
默认情况下，调度器的详情信息会被存储在内存，模式为：`RAMJobStore `，而且也不需要填写quartz.properties的配置。然而，如果是持久化的模式，那么quartz.properties就必须填写，因为文件中制定了信息存储模式和数据源信息。

```
# 线程调度器实例名
org.quartz.scheduler.instanceName = quartzScheduler
# 线程池的线程数，即最多3个任务同时跑
org.quartz.threadPool.threadCount = 3

# 如何存储任务和触发器等信息
org.quartz.jobStore.class = org.quartz.impl.jdbcjobstore.JobStoreTX
# 驱动代理
org.quartz.jobStore.driverDelegateClass = org.quartz.impl.jdbcjobstore.StdJDBCDelegate
# 表前缀
org.quartz.jobStore.tablePrefix = qrtz_ 
# 数据源
org.quartz.jobStore.dataSource = quartzDataSource
# 是否集群
org.quartz.jobStore.isClustered = false

# 数据源
# 驱动
org.quartz.dataSource.quartzDataSource.driver = com.mysql.cj.jdbc.Driver
# 连接URL
org.quartz.dataSource.quartzDataSource.URL = jdbc:mysql://localhost:3306/quartz?characterEncoding=utf-8&useSSL=true&&serverTimezone=Asia/Shanghai
# 用户名
org.quartz.dataSource.quartzDataSource.user = root
# 密码
org.quartz.dataSource.quartzDataSource.password = 123456
# 最大连接数
org.quartz.dataSource.quartzDataSource.maxConnections = 5
```
其他内容和`RAMJobStore`模式相同。

这里，记录下一个大坑：完成上面的配置之后，仍然无法连接到数据库，报错的内容是`C3P0的connectionProvider初始化失败`。查了很多资料，再结合错误信息，花了半天时间，才发现：原来quartz默认使用了C3P0的数据源，然而quartz的依赖包中并没有C3P0的依赖，因此无法初始化连接器。解决方法：只要在maven管理的POM.xml中把C3P0的依赖加上就可以了。

