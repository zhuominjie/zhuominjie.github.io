---
title: '微服务[Exception]：Zuul无法访问负载均衡服务'
tags:
  - zuul
  - eureka
  - micro service
  - spring cloud
keywords:
  - zuul
  - eureka
  - micro service
  - spring cloud
categories: Java
abbrlink: 8e887edc
date: 2018-05-15 14:28:11
---
## 问题描述
使用zuul的负载均衡功能时，无法访问负载均衡服务
#### 先决条件
1. 使用Eureka作为服务注册
2. zuul配置路由匹配规则为服务化的路由规则

<!--more-->
#### 异常如下
```
15:50:18.013 Gateway [http-nio-9090-exec-6] WARN  o.s.c.n.z.f.post.SendErrorFilter - Error during filtering
com.netflix.zuul.exception.ZuulException: Forwarding error
	at org.springframework.cloud.netflix.zuul.filters.route.RibbonRoutingFilter.handleException(RibbonRoutingFilter.java:189)
	at org.springframework.cloud.netflix.zuul.filters.route.RibbonRoutingFilter.forward(RibbonRoutingFilter.java:164)
	at org.springframework.cloud.netflix.zuul.filters.route.RibbonRoutingFilter.run(RibbonRoutingFilter.java:111)
	at com.netflix.zuul.ZuulFilter.runFilter(ZuulFilter.java:112)
	at com.netflix.zuul.FilterProcessor.processZuulFilter(FilterProcessor.java:193)
	at com.netflix.zuul.FilterProcessor.runFilters(FilterProcessor.java:157)
	at com.netflix.zuul.FilterProcessor.route(FilterProcessor.java:118)
	at com.netflix.zuul.ZuulRunner.route(ZuulRunner.java:96)
	at com.netflix.zuul.http.ZuulServlet.route(ZuulServlet.java:116)
	at com.netflix.zuul.http.ZuulServlet.service(ZuulServlet.java:81)
	at org.springframework.web.servlet.mvc.ServletWrappingController.handleRequestInternal(ServletWrappingController.java:157)
	at org.springframework.cloud.netflix.zuul.web.ZuulController.handleRequest(ZuulController.java:44)
	at org.springframework.web.servlet.mvc.SimpleControllerHandlerAdapter.handle(SimpleControllerHandlerAdapter.java:50)
	at org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:967)
	at org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:901)
	at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:970)
	at org.springframework.web.servlet.FrameworkServlet.doGet(FrameworkServlet.java:861)
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:635)
	at org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:846)
	at javax.servlet.http.HttpServlet.service(HttpServlet.java:742)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:231)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:52)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at org.springframework.boot.web.filter.ApplicationContextHeaderFilter.doFilterInternal(ApplicationContextHeaderFilter.java:55)
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:101)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at org.springframework.boot.actuate.trace.WebRequestTraceFilter.doFilterInternal(WebRequestTraceFilter.java:110)
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:317)
	at org.springframework.security.web.access.intercept.FilterSecurityInterceptor.invoke(FilterSecurityInterceptor.java:127)
	at org.springframework.security.web.access.intercept.FilterSecurityInterceptor.doFilter(FilterSecurityInterceptor.java:91)
	at org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:331)
	at org.springframework.security.web.access.ExceptionTranslationFilter.doFilter(ExceptionTranslationFilter.java:114)
	at org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:331)
	at org.springframework.security.web.session.SessionManagementFilter.doFilter(SessionManagementFilter.java:137)
	at org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:331)
	at org.springframework.security.web.authentication.AnonymousAuthenticationFilter.doFilter(AnonymousAuthenticationFilter.java:111)
	at org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:331)
	at org.springframework.security.web.servletapi.SecurityContextHolderAwareRequestFilter.doFilter(SecurityContextHolderAwareRequestFilter.java:170)
	at org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:331)
	at org.springframework.security.web.savedrequest.RequestCacheAwareFilter.doFilter(RequestCacheAwareFilter.java:63)
	at org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:331)
	at com.cmcc.cmct.gateway.auth.AuthenticationTokenProcessingFilter.doFilter(AuthenticationTokenProcessingFilter.java:47)
	at org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:331)
	at org.springframework.security.web.authentication.logout.LogoutFilter.doFilter(LogoutFilter.java:116)
	at org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:331)
	at org.springframework.web.filter.CorsFilter.doFilterInternal(CorsFilter.java:96)
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107)
	at org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:331)
	at org.springframework.security.web.header.HeaderWriterFilter.doFilterInternal(HeaderWriterFilter.java:64)
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107)
	at org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:331)
	at org.springframework.security.web.context.SecurityContextPersistenceFilter.doFilter(SecurityContextPersistenceFilter.java:105)
	at org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:331)
	at org.springframework.security.web.context.request.async.WebAsyncManagerIntegrationFilter.doFilterInternal(WebAsyncManagerIntegrationFilter.java:56)
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107)
	at org.springframework.security.web.FilterChainProxy$VirtualFilterChain.doFilter(FilterChainProxy.java:331)
	at org.springframework.security.web.FilterChainProxy.doFilterInternal(FilterChainProxy.java:214)
	at org.springframework.security.web.FilterChainProxy.doFilter(FilterChainProxy.java:177)
	at org.springframework.web.filter.DelegatingFilterProxy.invokeDelegate(DelegatingFilterProxy.java:347)
	at org.springframework.web.filter.DelegatingFilterProxy.doFilter(DelegatingFilterProxy.java:263)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at org.springframework.web.filter.RequestContextFilter.doFilterInternal(RequestContextFilter.java:99)
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at org.springframework.web.filter.HttpPutFormContentFilter.doFilterInternal(HttpPutFormContentFilter.java:108)
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at org.springframework.web.filter.HiddenHttpMethodFilter.doFilterInternal(HiddenHttpMethodFilter.java:81)
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at org.springframework.web.filter.CharacterEncodingFilter.doFilterInternal(CharacterEncodingFilter.java:197)
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at org.springframework.boot.actuate.autoconfigure.MetricsFilter.doFilterInternal(MetricsFilter.java:106)
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:107)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:193)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:166)
	at org.apache.catalina.core.StandardWrapperValve.invoke(StandardWrapperValve.java:199)
	at org.apache.catalina.core.StandardContextValve.invoke(StandardContextValve.java:96)
	at org.apache.catalina.authenticator.AuthenticatorBase.invoke(AuthenticatorBase.java:478)
	at org.apache.catalina.core.StandardHostValve.invoke(StandardHostValve.java:140)
	at org.apache.catalina.valves.ErrorReportValve.invoke(ErrorReportValve.java:81)
	at org.apache.catalina.core.StandardEngineValve.invoke(StandardEngineValve.java:87)
	at org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:342)
	at org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:803)
	at org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:66)
	at org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:868)
	at org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1459)
	at org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:49)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1149)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:624)
	at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61)
	at java.lang.Thread.run(Thread.java:748)
Caused by: com.netflix.client.ClientException: null
	at com.netflix.client.AbstractLoadBalancerAwareClient.executeWithLoadBalancer(AbstractLoadBalancerAwareClient.java:118)
	at org.springframework.cloud.netflix.zuul.filters.route.support.AbstractRibbonCommand.run(AbstractRibbonCommand.java:152)
	at org.springframework.cloud.netflix.zuul.filters.route.support.AbstractRibbonCommand.run(AbstractRibbonCommand.java:49)
	at com.netflix.hystrix.HystrixCommand$2.call(HystrixCommand.java:302)
	at com.netflix.hystrix.HystrixCommand$2.call(HystrixCommand.java:298)
	at rx.internal.operators.OnSubscribeDefer.call(OnSubscribeDefer.java:46)
	at rx.internal.operators.OnSubscribeDefer.call(OnSubscribeDefer.java:35)
	at rx.internal.operators.OnSubscribeLift.call(OnSubscribeLift.java:48)
	at rx.internal.operators.OnSubscribeLift.call(OnSubscribeLift.java:30)
	at rx.internal.operators.OnSubscribeLift.call(OnSubscribeLift.java:48)
	at rx.internal.operators.OnSubscribeLift.call(OnSubscribeLift.java:30)
	at rx.internal.operators.OnSubscribeLift.call(OnSubscribeLift.java:48)
	at rx.internal.operators.OnSubscribeLift.call(OnSubscribeLift.java:30)
	at rx.Observable.unsafeSubscribe(Observable.java:10151)
	at rx.internal.operators.OnSubscribeDefer.call(OnSubscribeDefer.java:51)
	at rx.internal.operators.OnSubscribeDefer.call(OnSubscribeDefer.java:35)
	at rx.internal.operators.OnSubscribeLift.call(OnSubscribeLift.java:48)
	at rx.internal.operators.OnSubscribeLift.call(OnSubscribeLift.java:30)
	at rx.Observable.unsafeSubscribe(Observable.java:10151)
	at rx.internal.operators.OnSubscribeDoOnEach.call(OnSubscribeDoOnEach.java:41)
	at rx.internal.operators.OnSubscribeDoOnEach.call(OnSubscribeDoOnEach.java:30)
	at rx.Observable.unsafeSubscribe(Observable.java:10151)
	at rx.internal.operators.OnSubscribeDoOnEach.call(OnSubscribeDoOnEach.java:41)
	at rx.internal.operators.OnSubscribeDoOnEach.call(OnSubscribeDoOnEach.java:30)
	at rx.internal.operators.OnSubscribeLift.call(OnSubscribeLift.java:48)
	at rx.internal.operators.OnSubscribeLift.call(OnSubscribeLift.java:30)
	at rx.Observable.unsafeSubscribe(Observable.java:10151)
	at rx.internal.operators.OnSubscribeDoOnEach.call(OnSubscribeDoOnEach.java:41)
	at rx.internal.operators.OnSubscribeDoOnEach.call(OnSubscribeDoOnEach.java:30)
	at rx.Observable.unsafeSubscribe(Observable.java:10151)
	at rx.internal.operators.OnSubscribeDoOnEach.call(OnSubscribeDoOnEach.java:41)
	at rx.internal.operators.OnSubscribeDoOnEach.call(OnSubscribeDoOnEach.java:30)
	at rx.Observable.unsafeSubscribe(Observable.java:10151)
	at rx.internal.operators.OnSubscribeDoOnEach.call(OnSubscribeDoOnEach.java:41)
	at rx.internal.operators.OnSubscribeDoOnEach.call(OnSubscribeDoOnEach.java:30)
	at rx.internal.operators.OnSubscribeLift.call(OnSubscribeLift.java:48)
	at rx.internal.operators.OnSubscribeLift.call(OnSubscribeLift.java:30)
	at rx.Observable.unsafeSubscribe(Observable.java:10151)
	at rx.internal.operators.OnSubscribeDefer.call(OnSubscribeDefer.java:51)
	at rx.internal.operators.OnSubscribeDefer.call(OnSubscribeDefer.java:35)
	at rx.Observable.unsafeSubscribe(Observable.java:10151)
	at rx.internal.operators.OnSubscribeMap.call(OnSubscribeMap.java:48)
	at rx.internal.operators.OnSubscribeMap.call(OnSubscribeMap.java:33)
	at rx.Observable.unsafeSubscribe(Observable.java:10151)
	at rx.internal.operators.OnSubscribeDoOnEach.call(OnSubscribeDoOnEach.java:41)
	at rx.internal.operators.OnSubscribeDoOnEach.call(OnSubscribeDoOnEach.java:30)
	at rx.internal.operators.OnSubscribeLift.call(OnSubscribeLift.java:48)
	at rx.internal.operators.OnSubscribeLift.call(OnSubscribeLift.java:30)
	at rx.Observable.unsafeSubscribe(Observable.java:10151)
	at rx.internal.operators.OnSubscribeDoOnEach.call(OnSubscribeDoOnEach.java:41)
	at rx.internal.operators.OnSubscribeDoOnEach.call(OnSubscribeDoOnEach.java:30)
	at rx.Observable.unsafeSubscribe(Observable.java:10151)
	at rx.internal.operators.OnSubscribeDefer.call(OnSubscribeDefer.java:51)
	at rx.internal.operators.OnSubscribeDefer.call(OnSubscribeDefer.java:35)
	at rx.internal.operators.OnSubscribeLift.call(OnSubscribeLift.java:48)
	at rx.internal.operators.OnSubscribeLift.call(OnSubscribeLift.java:30)
	at rx.Observable.subscribe(Observable.java:10247)
	at rx.Observable.subscribe(Observable.java:10214)
	at rx.internal.operators.BlockingOperatorToFuture.toFuture(BlockingOperatorToFuture.java:51)
	at rx.observables.BlockingObservable.toFuture(BlockingObservable.java:411)
	at com.netflix.hystrix.HystrixCommand.queue(HystrixCommand.java:378)
	at com.netflix.hystrix.HystrixCommand.execute(HystrixCommand.java:344)
	at org.springframework.cloud.netflix.zuul.filters.route.RibbonRoutingFilter.forward(RibbonRoutingFilter.java:158)
	... 105 common frames omitted
Caused by: java.lang.RuntimeException: java.net.UnknownHostException: xntest02.gzhl.quality: 未知的名称或服务
	at rx.exceptions.Exceptions.propagate(Exceptions.java:58)
	at rx.observables.BlockingObservable.blockForSingle(BlockingObservable.java:464)
	at rx.observables.BlockingObservable.single(BlockingObservable.java:341)
	at com.netflix.client.AbstractLoadBalancerAwareClient.executeWithLoadBalancer(AbstractLoadBalancerAwareClient.java:112)
	... 167 common frames omitted
Caused by: java.net.UnknownHostException: xntest02.gzhl.quality: 未知的名称或服务
	at java.net.Inet6AddressImpl.lookupAllHostAddr(Native Method)
	at java.net.InetAddress$2.lookupAllHostAddr(InetAddress.java:928)
	at java.net.InetAddress.getAddressesFromNameService(InetAddress.java:1323)
	at java.net.InetAddress.getAllByName0(InetAddress.java:1276)
	at java.net.InetAddress.getAllByName(InetAddress.java:1192)
	at java.net.InetAddress.getAllByName(InetAddress.java:1126)
	at org.apache.http.impl.conn.SystemDefaultDnsResolver.resolve(SystemDefaultDnsResolver.java:45)
	at org.apache.http.impl.conn.DefaultHttpClientConnectionOperator.connect(DefaultHttpClientConnectionOperator.java:112)
	at org.apache.http.impl.conn.PoolingHttpClientConnectionManager.connect(PoolingHttpClientConnectionManager.java:359)
	at org.apache.http.impl.execchain.MainClientExec.establishRoute(MainClientExec.java:381)
	at org.apache.http.impl.execchain.MainClientExec.execute(MainClientExec.java:237)
	at org.apache.http.impl.execchain.ProtocolExec.execute(ProtocolExec.java:185)
	at org.apache.http.impl.execchain.RetryExec.execute(RetryExec.java:89)
	at org.apache.http.impl.execchain.RedirectExec.execute(RedirectExec.java:111)
	at org.apache.http.impl.client.InternalHttpClient.doExecute(InternalHttpClient.java:185)
	at org.apache.http.impl.client.CloseableHttpClient.execute(CloseableHttpClient.java:83)
	at org.apache.http.impl.client.CloseableHttpClient.execute(CloseableHttpClient.java:108)
	at org.springframework.cloud.netflix.ribbon.apache.RibbonLoadBalancingHttpClient.execute(RibbonLoadBalancingHttpClient.java:82)
	at org.springframework.cloud.netflix.ribbon.apache.RibbonLoadBalancingHttpClient.execute(RibbonLoadBalancingHttpClient.java:42)
	at com.netflix.client.AbstractLoadBalancerAwareClient$1.call(AbstractLoadBalancerAwareClient.java:104)
	at com.netflix.loadbalancer.reactive.LoadBalancerCommand$3$1.call(LoadBalancerCommand.java:303)
	at com.netflix.loadbalancer.reactive.LoadBalancerCommand$3$1.call(LoadBalancerCommand.java:287)
	at rx.internal.util.ScalarSynchronousObservable$3.call(ScalarSynchronousObservable.java:231)
	at rx.internal.util.ScalarSynchronousObservable$3.call(ScalarSynchronousObservable.java:228)
	at rx.Observable.unsafeSubscribe(Observable.java:10151)
	at rx.internal.operators.OnSubscribeConcatMap$ConcatMapSubscriber.drain(OnSubscribeConcatMap.java:286)
	at rx.internal.operators.OnSubscribeConcatMap$ConcatMapSubscriber.onNext(OnSubscribeConcatMap.java:144)
	at com.netflix.loadbalancer.reactive.LoadBalancerCommand$1.call(LoadBalancerCommand.java:185)
	at com.netflix.loadbalancer.reactive.LoadBalancerCommand$1.call(LoadBalancerCommand.java:180)
	at rx.Observable.unsafeSubscribe(Observable.java:10151)
	at rx.internal.operators.OnSubscribeConcatMap.call(OnSubscribeConcatMap.java:94)
	at rx.internal.operators.OnSubscribeConcatMap.call(OnSubscribeConcatMap.java:42)
	at rx.Observable.unsafeSubscribe(Observable.java:10151)
	at rx.internal.operators.OperatorRetryWithPredicate$SourceSubscriber$1.call(OperatorRetryWithPredicate.java:127)
	at rx.internal.schedulers.TrampolineScheduler$InnerCurrentThreadScheduler.enqueue(TrampolineScheduler.java:73)
	at rx.internal.schedulers.TrampolineScheduler$InnerCurrentThreadScheduler.schedule(TrampolineScheduler.java:52)
	at rx.internal.operators.OperatorRetryWithPredicate$SourceSubscriber.onNext(OperatorRetryWithPredicate.java:79)
	at rx.internal.operators.OperatorRetryWithPredicate$SourceSubscriber.onNext(OperatorRetryWithPredicate.java:45)
	at rx.internal.util.ScalarSynchronousObservable$WeakSingleProducer.request(ScalarSynchronousObservable.java:276)
	at rx.Subscriber.setProducer(Subscriber.java:209)
	at rx.internal.util.ScalarSynchronousObservable$JustOnSubscribe.call(ScalarSynchronousObservable.java:138)
	at rx.internal.util.ScalarSynchronousObservable$JustOnSubscribe.call(ScalarSynchronousObservable.java:129)
	at rx.internal.operators.OnSubscribeLift.call(OnSubscribeLift.java:48)
	at rx.internal.operators.OnSubscribeLift.call(OnSubscribeLift.java:30)
	at rx.internal.operators.OnSubscribeLift.call(OnSubscribeLift.java:48)
	at rx.internal.operators.OnSubscribeLift.call(OnSubscribeLift.java:30)
	at rx.internal.operators.OnSubscribeLift.call(OnSubscribeLift.java:48)
	at rx.internal.operators.OnSubscribeLift.call(OnSubscribeLift.java:30)
	at rx.Observable.subscribe(Observable.java:10247)
	at rx.Observable.subscribe(Observable.java:10214)
	at rx.observables.BlockingObservable.blockForSingle(BlockingObservable.java:444)
	... 169 common frames omitted
```

#### 分析
根据如下两部分的异常内容，通常会认为是`Ribbon`和`Hystrix`的超时问题。

**异常一：**

```
com.netflix.zuul.exception.ZuulException: Forwarding error
    at org.springframework.cloud.netflix.zuul.filters.route.RibbonRoutingFilter.handleException(RibbonRoutingFilter.java:189)
    at org.springframework.cloud.netflix.zuul.filters.route.RibbonRoutingFilter.forward(RibbonRoutingFilter.java:164)
    at org.springframework.cloud.netflix.zuul.filters.route.RibbonRoutingFilter.run(RibbonRoutingFilter.java:111)
    ......
```
**异常二：**

```
Caused by: com.netflix.client.ClientException: null
    at com.netflix.client.AbstractLoadBalancerAwareClient.executeWithLoadBalancer(AbstractLoadBalancerAwareClient.java:118)
    at org.springframework.cloud.netflix.zuul.filters.route.support.AbstractRibbonCommand.run(AbstractRibbonCommand.java:152)
    at org.springframework.cloud.netflix.zuul.filters.route.support.AbstractRibbonCommand.run(AbstractRibbonCommand.java:49)
    at com.netflix.hystrix.HystrixCommand$2.call(HystrixCommand.java:302)
    at com.netflix.hystrix.HystrixCommand$2.call(HystrixCommand.java:298)
```
**相应的解决方案：**

```
ribbon.ConnectTimeout=60000
ribbon.ReadTimeout=60000

hystrix.command.default.execution.isolation.thread.timeoutInMilliseconds=120000
```

**然而**

不幸的是，我加了这两个超时配置之后，并没有改变zuul负载均衡转发异常。唯一改变的是：在每次异常发生时，请求的等待时间被大大延长。抓狂~~~~

通过多次的本地实验，我才开始对**异常三**产生了重视，内容如下：
**异常三：**

```
Caused by: java.net.UnknownHostException: xntest02.gzhl.quality: 未知的名称或服务
    at java.net.Inet6AddressImpl.lookupAllHostAddr(Native Method)
    at java.net.InetAddress$2.lookupAllHostAddr(InetAddress.java:928)
    at java.net.InetAddress.getAddressesFromNameService(InetAddress.java:1323)
```
是的，重点在这里。尽管负载均衡器在启动的时候就发现了可以用于分担压力的三个节点，然而，这些节点信息都是Eureka提供的。我们的当地主机虽然获得了这些节点的`hostname`，却根本不知道如何去访问它们。

**zuul启动时获取的服务注册信息**

```java
16:07:42.327 Gateway [main] INFO  c.n.l.DynamicServerListLoadBalancer - DynamicServerListLoadBalancer for client CloudAPI initialized: 
DynamicServerListLoadBalancer:{NFLoadBalancer:name=CloudAPI,current list of Servers=[xntest02.gzhl.quality:9000, xntest03.gzhl.quality:9000, xntest01.gzhl.quality:9000],
Load balancer stats=Zone stats: {defaultzone=[Zone:defaultzone;	
Instance count:3;	
Active connections count: 0;	
Circuit breaker tripped count: 0;	
Active connections per server: 0.0;]
},
Server stats: 
[[Server:xntest03.gzhl.quality:9000;	Zone:defaultZone;	Total Requests:0;	Successive connection failure:0;	Total blackout seconds:0;	Last connection made:Thu Jan 01 08:00:00 CST 1970;	First connection made: Thu Jan 01 08:00:00 CST 1970;	Active Connections:0;	total failure count in last (1000) msecs:0;	average resp time:0.0;	90 percentile resp time:0.0;	95 percentile resp time:0.0;	min resp time:0.0;	max resp time:0.0;	stddev resp time:0.0]
, [Server:xntest01.gzhl.quality:9000;	Zone:defaultZone;	Total Requests:0;	Successive connection failure:0;	Total blackout seconds:0;	Last connection made:Thu Jan 01 08:00:00 CST 1970;	First connection made: Thu Jan 01 08:00:00 CST 1970;	Active Connections:0;	total failure count in last (1000) msecs:0;	average resp time:0.0;	90 percentile resp time:0.0;	95 percentile resp time:0.0;	min resp time:0.0;	max resp time:0.0;	stddev resp time:0.0]
, [Server:xntest02.gzhl.quality:9000;	Zone:defaultZone;	Total Requests:0;	Successive connection failure:0;	Total blackout seconds:0;	Last connection made:Thu Jan 01 08:00:00 CST 1970;	First connection made: Thu Jan 01 08:00:00 CST 1970;	Active Connections:0;	total failure count in last (1000) msecs:0;	average resp time:0.0;	90 percentile resp time:0.0;	95 percentile resp time:0.0;	min resp time:0.0;	max resp time:0.0;	stddev resp time:0.0]
]}
```

看到这儿，解决方案也就出来了。那就是，确保每一个服务所在的服务器节点都能通过`hostname`和zuul网关互通。
从操作角度来说，就是要在每一台服务节点上配置其他所有服务节点的`hostname`，使得这些服务集群互通。

Liunx解决方案：
第一步 打开配置文件

```
vi /etc/hosts
```
第二步 增加`服务`所在`主机ip`对应的`hostname`

```
172.23.25.1 xntest01
172.23.25.2 xntest02
172.23.25.3 xntest03
```

**大功告成~~~**

 