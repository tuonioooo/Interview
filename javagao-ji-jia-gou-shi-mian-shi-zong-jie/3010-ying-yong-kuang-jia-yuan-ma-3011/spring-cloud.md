# SpringCloud

[SpringCloud源码解读](https://github.com/tuonioooo/micro-services-subject/blob/master/springcloudshi-zhan-pian.md)

**1. 什么是 spring cloud？**

spring cloud 是一系列框架的有序集合。它利用 spring boot 的开发便利性巧妙地简化了分布式系统基础设施的开发，如服务发现注册、配置中心、消息总线、负载均衡、断路器、数据监控等，都可以用 spring boot 的开发风格做到一键启动和部署。

**2. spring cloud 断路器的作用是什么？**

在分布式架构中，断路器模式的作用也是类似的，当某个服务单元发生故障（类似用电器发生短路）之后，通过断路器的故障监控（类似熔断保险丝），向调用方返回一个错误响应，而不是长时间的等待。这样就不会使得线程因调用故障服务被长时间占用不释放，避免了故障在分布式系统中的蔓延。

**3. spring cloud 的核心组件有哪些？**

* Eureka：服务注册于发现。
* Feign：基于动态代理机制，根据注解和选择的机器，拼接请求 url 地址，发起请求。
* Ribbon：实现负载均衡，从一个服务的多台机器中选择一台。
* Hystrix：提供线程池，不同的服务走不同的线程池，实现了不同服务调用的隔离，避免了服务雪崩的问题。
* Zuul：网关管理，由 Zuul 网关转发请求给对应的服务。

**4.springboot devtools热部署的原理**

spring-boot-devtools其深层原理是使用了两个ClassLoader，一个加载不会改变的类（jar包），另一个加载会改变的类。称为restartClassLoader，当代码块修改时，新的restartClassLoader类将代替旧的。

引用原文：[https://www.jianshu.com/p/c21f4ad3ab5a](https://www.jianshu.com/p/c21f4ad3ab5a)

**5.Spring Cloud Eureka自我保护机制（服务无法剔除）**

引用原文：[https://blog.csdn.net/weixin\_38187317/article/details/81231771](https://blog.csdn.net/weixin_38187317/article/details/81231771)



