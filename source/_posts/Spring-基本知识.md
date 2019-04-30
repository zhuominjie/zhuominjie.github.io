---
title: Spring：基本知识
abbrlink: c157b8b0
date: 2018-08-17 09:31:24
tags: Spring
keywords: Spring
categories: Spring
description:
---
## Spring
Spring 是 2003 年兴起的一个轻量级的 Java 开发框架，由 Rod Johnson 创建。它解决了业务逻辑层和其他各层的松耦合问题，并将面向接口的编程思想贯穿整个系统应用。

简单来说，Spring 是一个分层的 JavaSE/EE Full-Stack（一站式） 轻量级开源框架。为什么说 spring 是分层、一站式、轻量级的框架呢？

首先看分层。JavaEE 经典的 MVC 三层结构为表现层、业务层、持久层，Web表现层负责页面数据显示、页面跳转调度，例如 JSP/Servlet、SpringMVC；

Service 业务层负责业务处理、功能逻辑和事务控制，例如 Service、JavaBean、EJB；而持久层Dao则负责数据存取和封装，及与数据库打交道，例如 JDBC、Hibernate、Mybatis。
<!--more-->

而一站式，则指 Spring 为 JavaEE 的每一层都提供了解决方案，比如：

* 表现层：Struts1、Struts2、Spring MVC；

* 业务层：IoC 控制反转、AOP 面向切面编程、事务控制；

* 持久层：JdbcTemplate、HibernateTemplate、ORM 框架（对象关系映射）的整合。

至于轻量，则是指从大小与开销两方面而言，Spring都是轻量的。完整的 Spring 框架可以在一个大小只有 1MB 多的 Jar 文件里发布。并且 Spring 所需的处理开销也是微不足道的。

Spring 的出现解决了 EJB 臃肿、低效、繁琐复杂、脱离现实的情况。而且使用 Spring 编程是非侵入式的。Spring 应用中的对象不依赖于 Spring 的特定类。

## Spring 的体系结构
Spring 框架是一个分层架构，它包含一系列的功能要素，被分为大约20个模块。这些模块分为 Core Container、Data Access/Integration、Web、AOP、Aspects、Instrumentation 和 Test，如下图所示：

![Spring-arc.png](https://i.loli.net/2019/04/30/5cc7ba9832fe0.png)

核心容器（Core Container）包括 Core、Beans、Context、EL 模块。

1. Core 和 Beans 模块提供了 Spring 最基础的功能，Core 模块是核心，为其他模块提供支持，包括 Spring 的核心工具类。Beans 是 Spring 管理实体类 Bean 的主要模块，提供 IoC 控制反转和依赖注入 DI。

控制反转即将控制权由原来的程序员自己管理交给 Spring 来管理，依赖注入就是注入对象实例，有四种方式，即接口注入、setter 方法注入、构造器注入和注解注入。

2. Context 上下文模块，主要基于 Core 和 Beans 模块，Context 模块的 Context 包继承了 Beans 包的功能，还增加了国际化（I18N）、事件传播等，Context 上下文模块，还包括企业服务，例如 JNDI、EJB、电子邮件、国际化、校验和调度功能。

3. Expression Language，表达式语言模块，又称 SpEL，提供了在运行期间查询和操作对象图的强大能力。包含五个主要特性：（1）使用 Bean 的 ID 引用 Bean；（2）调用方法和访问对象的属性；（3）对值进行算术，关系和逻辑运算；（4）正则表达式匹配；（5）集合操作。

## Spring 的三大核心
这三大核心分别为 IOC（Inverse of Control 控制反转）、DI（Dependency Injection，依赖注入）和AOP（Aspect Oriented Programming 面向切面编程）。

#### 1. IOC（Inverse of Control 控制反转）

IOC，即将对象创建权利交给 Spring 工厂进行管理。比如说

```java
Content content = new Content();
```

现在，可以这样写:

```java
Content content = ContentFactory.getContent();
```

#### 2. DI（Dependency Injection，依赖注入）

DI 是指在 Spring 框架创建 Bean 对象时，动态地将依赖对象注入到 Bean 组件。简单的说，就是将另外一个 Bean 对象动态地注入到另一个 Bean 中。

DI 的做法是：由 Spring 容器创建 Service、Dao 对象，并且通过注解或配置将 Dao 传入 Servcie，那么 Service 对象就包含了 Dao 对象的引用。比如：

```java
@Service
public class UserServiceImpl implements UserService {
	@Autowire //通过注解注入对象
	private UserMapper userMapper;
}
```

#### 3. AOP（Aspect Oriented Programming 面向切面编程）

AOP 采取横向抽取机制，取代了传统纵向继承体系重复性代码的编写方式（例如性能监视、事务管理、安全检查、缓存、日志记录等）。

AOP 基于代理思想，对原来目标对象，创建代理对象，在不修改原对象代码情况下，通过代理对象，调用增强功能的代码，从而对原有业务方法进行增强。例如可以在插入 User 对象之前进行打印日志，请看下面的代码示例。

UserService.java

```java
public interface UserService {
	void add(User user);
}
```

前置增强类 PrintLogBefore.java

```java
//前置增强代码
public class PrintLogBefore implements MehtodBeforeAdvice {
	private static final Logger log = Logger.getLogger(PrintLogBefore.class);
	
	@Override
	public void before(Method method, Object[] arguments, Object target) throws Throwable {
		log.info("在插入User之前执行的方法");
	}
}
```

最后配置切入点：

```xml
<bean id="printBefore" class="com.example.PrintLogBefore"></bean>
	<aop:config>
		<aop:pointcut expression="execution(public void add(com.example.entity.User))" id="pointcut />
		<aop:advisor advice-ref="printBefore" pointcut-ref="pointcut"/>
	</aop:config>
```
这样在调用 add(User user) 方法之前就会打印如下内容：

```
“在插入User之前执行的方法”
```
