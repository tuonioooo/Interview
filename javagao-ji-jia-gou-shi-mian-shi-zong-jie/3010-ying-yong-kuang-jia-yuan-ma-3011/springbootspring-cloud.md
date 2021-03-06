# SpringBoot

[SpringBoot源码解读](https://tuonioooo.gitbooks.io/application-framework/content/springbootpian.html)

相关面试总结：[https://www.cnblogs.com/tangjian07/p/10802148.html](https://www.cnblogs.com/tangjian07/p/10802148.html)

1**. 什么是 spring boot？**

springboot 是为 spring 服务的，是用来简化新 spring 应用初始搭建以及开发过程的。

Spring Boot 已经建立在现有 spring 框架之上。使用 spring 启动，我们避免了之前我们必须做的所有样板代码和配置。因此，Spring Boot 可以 帮助我们以最少的工作量，更加健壮地使用现有的 Spring 功能。

2**. 为什么要用 spring boot？**

* 配置简单
* 独立运行
* 自动装配
* 无代码生产和 .xml 配置
* 提供应用监控
* 易上手
* 开发效率高

3**. spring boot 核心配置文件是什么？**

spring boot 核心的两个配置文件：

* * bootstrap \(. yml 或者 . properties\)：boostrap 由父 ApplicationContext 加载的，比 applicaton 优先加载，且 boostrap 里面的属性不能被覆盖；
  * application \(. yml 或者 . properties\)：用于 spring boot 项目的自动化配置。

4**. spring boot 配置文件有哪几种类型？它们有什么区别？**

配置文件有. properties 格式和 . yml 格式，它们主要的区别是书法风格不同。

. properties 配置如下：

```text
spring.RabbitMQ.port=5672
```

. yml 配置如下：

```text
spring:
 RabbitMQ:
    port: 5672
```

. yml 格式不支持 @PropertySource 注解导入。

**5. spring boot 有哪些方式可以实现热部署？**

* 使用 devtools  启动热部署，添加 devtools 库，在配置文件中把 spring.devtools.restart.enabled 设置为 true。
* 使用 Intellij IDE 编辑器，勾上自动编译或者手动重新编译。
* jrebel
* Spring Loaded jar包

> ## SpringBoot热部署原理 <a id="articleContentId"></a>
>
> spring-boot-devtools 是一个为开发者服务的一个模块，其中最重要的功能就是自动应用代码更改到最新的App上面去。原理是在发现代码有更改之后，重新启动应用，但是速度比手动停止后再启动还要更快，更快指的不是节省出来的手工操作的时间。 其深层原理是使用了两个ClassLoader，一个Classloader加载那些不会改变的类（第三方Jar包），另一个ClassLoader加载会更改的类，称为 restart ClassLoader ,这样在有代码更改的时候，原来的restart ClassLoader 被丢弃，重新创建一个restart ClassLoader，由于需要加载的类相比较少，所以实现了较快的重启时间（5秒以内）。
>
> Devtools原理

> 1. devtools会监听classpath下的文件变动，并且会立即重启应用（发生在保存时机），注意：因为其采用的虚拟机机制，该项重启是很快的。
> 2. devtools可以实现页面热部署（即页面修改后会立即生效，这个可以直接在application.properties文件中配置spring.thymeleaf.cache=false来实现\(这里注意不同的模板配置不一样\)

**6. jpa 和 hibernate 有什么区别？**

jpa 全称 Java Persistence API，是 Java 持久化接口规范，hibernate 属于 jpa 的具体实现。

#### 7.Spring Boot 中的监视器是什么？

Spring boot actuator 是 spring 启动框架中的重要功能之一。Spring boot 监视器可帮助您访 问生产环境中正在运行的应用程序的当前状态。有几个指标必须在生产环境中进行检查和 监控。即使一些外部应用程序可能正在使用这些服务来向相关人员触发警报消息。监视器 模块公开了一组可直接作为 HTTP URL 访问的 REST 端点来检查状态。

#### 8.如何在 Spring Boot 中禁用 Actuator 端点安全性？

默认情况下，所有敏感的 HTTP 端点都是安全的，只有具有 ACTUATOR 角色的用户才能访 问它们。安全性是使用标准的 HttpServletRequest.isUserInRole 方法实施的。 我们可以使用 management.security.enabled = false 来禁用安全性。只有在执行机构端点在防火墙后访问时，才建议禁用安全性。

#### 9.如何实现 Spring Boot 应用程序的安全性？

为了实现 Spring Boot 的安全性，我们使用 spring-boot-starter-security 依赖项，并且必须添 加安全配置。它只需要很少的代码。配置类将必须扩展 WebSecurityConfigurerAdapter 并覆 盖其方法。

#### 10.如何集成 Spring Boot 和 ActiveMQ？ 

对于集成 Spring Boot 和 ActiveMQ，我们使用 spring-boot-starter-activemq 依赖关系。 它只需要很少的配置，并且不需要样板代码。

#### 11.如何使用 Spring Boot 实现分页和排序？ 

使用 Spring Boot 实现分页非常简单。使用 Spring Data-JPA 可以实现将可分页的org.springframework.data.domain.Pageable 传递给存储库方法。

#### 12.什么是 Swagger？你用 Spring Boot 实现了它吗？

Swagger 广泛用于可视化 API，使用 Swagger UI 为前端开发人员提供在线沙箱。Swagger 是用于生成 RESTful Web 服务的可视化表示的工具，规范和完整框架实现。它使文档能够以 与服务器相同的速度更新。当通过 Swagger 正确定义时，消费者可以使用最少量的实现逻 辑来理解远程服务并与其进行交互。因此，Swagger 消除了调用服务时的猜测。

#### 13.什么是 Spring Profiles？

Spring Profiles 允许用户根据配置文件（dev，test，prod 等）来注册 bean。因此，当应用 程序在开发中运行时，只有某些 bean 可以加载，而在 PRODUCTION 中，某些其他 bean 可 以加载。假设我们的要求是 Swagger 文档仅适用于 QA 环境，并且禁用所有其他文档。这 可以使用配置文件来完成。Spring Boot 使得使用配置文件非常简单。

#### 14.如何使用 Spring Boot 实现异常处理？ 

Spring 提供了一种使用 ControllerAdvice 处理异常的非常有用的方法。 我们通过实现一个 ControlerAdvice 类，来处理控制器类抛出的所有异常。

#### 15.什么是 CSRF 攻击？ 

CSRF 代表跨站请求伪造。这是一种攻击，迫使最终用户在当前通过身份验证的 Web 应用 程序上执行不需要的操作。CSRF 攻击专门针对状态改变请求，而不是数据窃取，因为攻击 者无法查看对伪造请求的响应

#### 16.什么是YAML？

YAML是一种人类可读的数据序列化语言。它通常用于配置文件。

与属性文件相比，如果我们想要在配置文件中添加复杂的属性，YAML文件就更加结构化，而且更少混淆。可以看出YAML具有分层配置数据。

#### 17.运行Spring Boot有哪几种方式？

1）打包用命令或者放到容器中运行

2）用Maven/Gradle插件运行

3）直接执行main方法运行

#### 18.开启Spring Boot特性有哪几种方式？

1）继承spring-boot-starter-parent项目

2）导入spring-boot-dependencies项目依赖

#### 19.为什么我们不建议在实际的应用程序中使用 Spring Data Rest?

我们认为 Spring Data Rest 很适合快速原型制造！在大型应用程序中使用需要谨慎。

通过 Spring Data REST 你可以把你的数据实体作为 RESTful 服务直接发布。

当你设计 RESTful 服务器的时候，最佳实践表明，你的接口应该考虑到两件重要的事情：

你的模型范围。

你的客户。

通过 With Spring Data REST，你不需要再考虑这两个方面，只需要作为 TEST 服务发布实体。







\*\*\*\*

