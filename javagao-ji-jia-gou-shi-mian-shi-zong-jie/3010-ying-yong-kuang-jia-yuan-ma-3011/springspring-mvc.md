# Spring

[Spring源码解读](https://app.gitbook.com/@tuonioooo-notebook/s/application-framework/springyuan-ma-jie-du-pian)

[Spring MVC源码解读](https://tuonioooo.gitbooks.io/application-framework/content/springyuan-ma-jie-du-pian/springmvcyuan-ma-jie-du.html)

相关面试总结：[https://www.cnblogs.com/tangjian07/p/10802148.html](https://www.cnblogs.com/tangjian07/p/10802148.html)

1**. 为什么要使用 spring？**

* spring 提供了 IOC 技术，spring 容器会帮你管理依赖的对象，从而不需要自己创建和管理依赖对象了，更轻松的实现了程序的解耦。
* spring 提供了事务支持，使得事务操作变得更加方便。
* spring 提供了面向切面编程，这样可以更方便的处理某一类问题。
* 更方便的框架集成，spring 可以很方便的整合其他框架，比如 mybatis、hibernate等。

2**. 解释一下什么是 aop？**

aop 是面向切面编程，通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。

简单来说就是统一处理某一“切面”（类）的问题的编程思想，比如统一处理日志、异常等。

3**. 解释一下什么是 ioc？**

ioc：Inversion of Control（即控制反转），是spring 的核心，对于 spring 框架来讲，就是由 spring 来负责控制对象的生命周期和对象间的关系。

简单来说，控制指的是当前对象对内部成员的控制权；控制反转指的是，这种控制权不由当前对象管理了，由其他（类，第三方容器）来管理。

4**. spring 有哪些主要模块？**

* spring core：框架最基础的部分，提供 IOC 和依赖注入特性。
* spring context：构建于core 封装包基础上的 context 封装包，提供了一种框架式的对象访问方法。
* spring dao：Data Access Object 提供了 JDBC 的抽象层。
* spring aop：提供了面向切面的编程实现，让你可以自定义拦截器、切点等。
* spring Web：提供了针对 Web 开发的集成特性，例如文件上传，利用 servlet listeners 进行 ioc 容器初始化和针对 Web 的 ApplicationContext。
* spring Web mvc：spring 中的 mvc 封装包提供了 Web 应用的 Model-View-Controller（MVC）的实现。

5**. spring 常用的注入方式有哪些？**

* setter 属性注入
* 构造方法注入
* 注解方式注入

6**. spring 中的 bean 是线程安全的吗？**

spring 中的 bean 默认是单例模式，spring 框架并没有对单例 bean 进行多线程的封装处理。

实际上大部分时候spring bean 无状态的（比如 dao 类），所有某种程度上来说 bean 也是安全的，但如果 bean 有状态的话（比如 view model 对象），那就要开发者自己去保证线程安全了，最简单的就是改变 bean 的作用域，把“singleton”变更为“prototype”，这样请求 bean 相当于 new Bean\(\)了，所以就可以保证线程安全了。

常见的有两种解决办法: 

1. 在Bean对象中尽量避免定义可变的成员变量（不太 现实）

2. 在类中定义一个ThreadLocal成员变量，将需要的可变成员变量保存在ThreadLocal中 （推荐的一种方式）。

7**. spring 支持几种 bean 的作用域？**

spring 支持 5 种作用域，如下：

* * singleton：spring ioc 容器中只存在一个 bean 实例，bean 以单例模式存在，是系统默认值；
  * prototype：每次从容器调用 bean 时都会创建一个新的示例，既每次 getBean\(\)相当于执行 new Bean\(\)操作；
  * request：在请求 bean 范围内会每一个来自客户端的网络请求创建一个实例，在请求完成以后， bean 会失效并被垃圾回收器回收；
  * session：同一个 http session 共享一个 bean 实例；
  * global-session：用于 portlet 容器，因为每个 portlet 有单独的 session，globalsession 提供一个全局性的 http session。

**注意：** 使用 prototype 作用域需要慎重的思考，因为频繁创建和销毁 bean 会带来很大的性能开销。

8**. spring 自动装配 bean 有哪些方式？**

* no：默认值，表示没有自动装配，应使用显式 bean 引用进行装配。
* byName：它根据 bean 的名称注入对象依赖项。
* byType：它根据类型注入对象依赖项。
* 构造函数：通过构造函数来注入依赖项，需要设置大量的参数。
* autodetect：容器首先通过构造函数使用 autowire 装配，如果不能，则通过 byType 自动装配。

9**. spring 事务实现方式有哪些？**

* 声明式事务：声明式事务也有两种实现方式，基于

  xml 配置文件的方式和注解方式（在类上添加 @Transaction 注解）。

* 编码方式：提供编码的形式管理和维护事务。

10**. 说一下 spring 的事务隔离？**

spring 有五大隔离级别，默认值为 ISOLATION\_DEFAULT（使用数据库的设置），其他四个隔离级别和数据库的隔离级别一致：

* ISOLATION\_DEFAULT：用底层数据库的设置隔离级别，数据库设置的是什么我就用什么；
* ISOLATIONREADUNCOMMITTED：未提交读，最低隔离级别、事务未提交前，就可被其他事务读取（会出现幻读、脏读、不可重复读）；
* ISOLATIONREADCOMMITTED：提交读，一个事务提交后才能被其他事务读取到（会造成幻读、不可重复读），SQL server 的默认级别；
* ISOLATIONREPEATABLEREAD：可重复读，保证多次读取同一个数据时，其值都和事务开始时候的内容是一致，禁止读取到别的事务未提交的数据（会造成幻读），MySQL 的默认级别；
* ISOLATION\_SERIALIZABLE：序列化，代价最高最可靠的隔离级别，该隔离级别能防止脏读、不可重复读、幻读。

**脏读** ：表示一个事务能够读取另一个事务中还未提交的数据。比如，某个事务尝试插入记录 A，此时该事务还未提交，然后另一个事务尝试读取到了记录 A。

**不可重复读** ：一个事务范围内两个相同的查询却返回了不同数据。

**幻读** ：指同一个事务内多次查询返回的结果集不一样。比如同一个事务 A 第一次查询时候有 n 条记录，但是第二次同等条件下查询却有 n+1 条记录，这就好像产生了幻觉。发生幻读的原因也是另外一个事务新增或者删除或者修改了第一个事务结果集里面的数据，同一个记录的数据内容被修改了，所有数据行的记录就变多或者变少了。

#### **11.** BeanFactory 和 ApplicationContext 有什么区别？

BeanFactory 可以理解为含有 bean 集合的工厂类。BeanFactory 包含了种 bean 的定义，以便 在接收到客户端请求时将对应的 bean 实例化。 BeanFactory 还能在实例化对象的时生成协作类之间的关系。此举将 bean 自身与 bean 客户端的 配置中解放出来。BeanFactory 还包含 了 bean 生命周期的控制，调用客户端的初始化方法 （initialization methods）和销毁方法（destruction methods）。 从表面上看，application context 如同 bean factory 一样具有 bean 定义、bean 关联关系的设 置，根据请求分发 bean 的功能。但 applicationcontext 在此基础上还提供了其他的功能，如下：

* 提供了支持国际化的文本消息 
*  统一的资源文件读取方式 
*  已在监听器中注册的 bean 的事件

以下是三种较常见的 ApplicationContext 实现方式： 

1. ClassPathXmlApplicationContext：从 classpath 的 XML 配置文件中读取上下文，并生成上 下文定义。应用程序上下文从程序环境变量中 ApplicationContext context = new ClassPathXmlApplicationContext\(“bean.xml”\); 
2. FileSystemXmlApplicationContext ：由文件系统中的 XML 配置文件读取上下文。 ApplicationContext context = new FileSystemXmlApplicationContext\(“bean.xml”\); 
3. XmlWebApplicationContext：由 Web 应用的 XML 文件读取上下文。
4. AnnotationConfigApplicationContext\(基于 Java 配置启动容器\)

![](../../.gitbook/assets/image%20%284%29.png)

#### 12.Spring 有几种配置方式？

* 基于 XML 的配置 

在 Spring 框架中，依赖和服务需要在专门的配置文件来实现，我常用的 XML 格式的配置文件。这 些配置文件的格式通常用开头，然后一系列的 bean 定义和专门的应用配置 选项组成。 SpringXML 配置的主要目的时候是使所有的 Spring 组件都可以用 xml 文件的形式来进行配置。这 意味着不会出现其他的 Spring 配置类型（比如声明的方式或基于 Java Class 的配置方式） Spring 的 XML 配置方式是使用被 Spring 命名空间的所支持的一系列的 XML 标签来实现的。 Spring 有以下主要的命名空间：context、beans、jdbc、tx、aop、mvc 和 aso。 如：

```text
<beans> 
 <!-- JSON Support --> 
 <bean name="viewResolver"
class="org.springframework.web.servlet.view.BeanNameViewResolver"/> 
 <bean name="jsonTemplate"
class="org.springframework.web.servlet.view.json.MappingJackson2JsonV
iew"/> 
 <bean id="restTemplate"
class="org.springframework.web.client.RestTemplate"/> 
</beans>
```

下面这个 web.xml 仅仅配置了 DispatcherServlet，这件最简单的配置便能满足应用程序配置运 行时组件的需求。

```text
<web-app> 
 <display-name>Archetype Created Web Application</display-name> 
 <servlet> 
 <servlet-name>spring</servlet-name> 
 <servletclass>org.springframework.web.servlet.DispatcherServlet</servletclass> 
 <load-on-startup>1</load-on-startup> 
 </servlet>
```

* 基于注解的配置 

Spring 对 Java 配置的支持是由@Configuration 注解和@Bean 注解来实现的。由@Bean 注解 的方法将会实例化、配置和初始化一个 新对象，这个对象将由 Spring 的 IoC 容器来管理。 @Bean 声明所起到的作用与 元素类似。被 @Configuration 所注解的类则表示这个类 的主要目的是作为 bean 定义的资源。被@Configuration 声明的类可以通过在同一个类的 内部调 用@bean 方法来设置嵌入 bean 的依赖关系。 最简单的@Configuration 声明类请参考下面的代码：

```text
@Configuration 
public class AppConfig{ 
 @Bean 
 public MyService myService() { 
 return new MyServiceImpl(); 
 } 
}
```

对于上面的@Beans 配置文件相同的 XML 配置文件如下：

```text
<beans> 
 <bean id="myService" class="com.somnus.services.MyServiceImpl"/> 
</beans>
```

上述配置方式的实例化方式如下：利用 AnnotationConfigApplicationContext 类进行实例化

```text
public static void main(String[] args) { 
 ApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig.class); 
 MyService myService = ctx.getBean(MyService.class); 
 myService.doStuff();
 }
```

要使用组件组建扫描，仅需用@Configuration 进行注解即可：

```text
@Configuration 
@ComponentScan(basePackages = "com.somnus") 
public class AppConfig { 
 ... 
}
```



* 基于 Java 的配置

#### 13.请解释 Spring Bean 的生命周期？

Spring Bean 的生命周期简单易懂。在一个 bean 实例被初始化时，需要执行一系列的初始化操作 以达到可用的状态。同样的，当一个 bean 不在被调用时需要进行相关的析构操作，并从 bean 容 器中移除。 Spring bean factory 负责管理在 spring 容器中被创建的 bean 的生命周期。

Bean 的生命周期 由两组回调（call back）方法组成

1. 初始化之后调用的回调方法。 
2. 销毁之前调用的回调方法。 

Spring 框架提供了以下四种方式来管理 bean 的生命周期事件： 

* InitializingBean 和 DisposableBean 回调接口
*  针对特殊行为的其他 Aware 接口 
* Bean 配置文件中的 Custom init\(\)方法和 destroy\(\)方法
* @PostConstruct 和@PreDestroy 注解方式

#### 14.什么是 Spring inner beans？

在 Spring 框架中，无论何时 bean 被使用时，当仅被调用了一个属性。一个明智的做法是将这个 bean 声明为内部 bean。内部 bean 可以用 setter 注入“属性”和构造方法注入“构造参数”的方式来 实现。 比如，在我们的应用程序中，一个 Customer 类引用了一个 Person 类，我们的要做的是创建一个 Person 的实例，然后在 Customer 内部使用

```text
public class Customer{ 
 private Person person; 
 //Setters and Getters 
} 
public class Person{ 
 private String name; 
 private String address; 
 private int age; 
 //Setters and Getters 
}
```

内部 bean 的声明方式如下：

```text
<bean id="CustomerBean" class="com.somnus.common.Customer"> 
 <property name="person"> 
 <!-- This is inner bean --> 
 <bean class="com.howtodoinjava.common.Person"> 
 <property name="name" value="lokesh" /> 
 <property name="address" value="India" /> 
 <property name="age" value="34" /> 
 </bean> 
 </property> 
</bean>
```

#### 15.请举例说明如何在 Spring 中注入一个 Java Collection？

Spring 提供了以下四种集合类的配置元素：

*  &lt;list&gt; : 该标签用来装配可重复的 list 值。 
* &lt;set&gt; : 该标签用来装配没有重复的 set 值。
* &lt;map&gt; : 该标签可用来注入键和值可以为任何类型的键值对。
* &lt;props&gt;: 该标签支持注入键和值都是字符串类型的键值对。

#### 16.构造方法注入和设值注入有什么区别？

* 在设值注入方法支持大部分的依赖注入，如果我们仅需 要注入 int、string 和 long 型的变量，我 们不要用设值的方法注入。对于基本类型，如果我们没有注入的话，可以为基本类型设置默认值。 在构造方法 注入不支持大部分的依赖注入，因为在调用构造方法中必须传入正确的构造参数，否则 的话为报错。
* 设值注入不会重写构造方法的值。如果我们对同一个变量同时使用了构造方法注入又使用了设置方 法注入的话，那么构造方法将不能覆盖由设值方法注入的值。很明显，因为构造方法尽在对象被创 建时调用。
* 在使用设值注入时有可能还不能保证某种依赖是否已经被注入，也就是说这时对象的依赖关系有可 能是不完整的。而在另一种情况下，构造器注入则不允许生成依赖关系不完整的对象。
* 在设值注入时如果对象 A 和对象 B 互相依赖，在创建对象 A 时 Spring 会抛出 sObjectCurrentlyInCreationException 异常，因为在 B 对象被创建之前 A 对 象是不能被创建的，反之亦然。所以 Spring 用设值注入的方法解决了循环依赖 的问题，因对象的设值方法是在对象被创建之前被调用的。

#### 17.Spring 框架中有哪些不同类型的事件？

Spring 的 ApplicationContext 提供了支持事件和代码中监听器的功能。

我们可以创建 bean 用来监听在 ApplicationContext 中发布的事件。ApplicationEvent 类和在 ApplicationContext 接口中处理的事件，如果一个 bean 实现了 ApplicationListener 接口，当一个 ApplicationEvent 被发布以后，bean 会自动被通知.

```text
public class AllApplicationEventListener implements ApplicationListener
<ApplicationEvent>{ 
 @Override 
 public void onApplicationEvent(ApplicationEvent applicationEvent) 
 { 
 //process event 
 } 
}
```

Spring 提供了以下 5 中标准的事件：

1. 上下文更新事件（ContextRefreshedEvent）：该事件会在 ApplicationContext 被初始化或者 更新时发布。也可以在调用 ConfigurableApplicationContext 接口中的 refresh\(\)方法时被触 发。 
2. 上下文开始事件（ContextStartedEvent）：当容器调用 ConfigurableApplicationContext 的 Start\(\)方法开始/重新开始容器时触发该事件。
3. 上下文停止事件（ContextStoppedEvent）：当容器调用 ConfigurableApplicationContext 的 Stop\(\)方法停止容器时触发该事件。
4. 上下文关闭事件（ContextClosedEvent）：当 ApplicationContext 被关闭时触发该事件。容器 被关闭时，其管理的所有单例 Bean 都被销毁。
5. 请求处理事件（RequestHandledEvent）：在 Web 应用中，当一个 http 请求（request）结束 触发该事件。

除了上面介绍的事件以外，还可以通过扩展 ApplicationEvent 类来开发自定义的事件。

```text
public class CustomApplicationEvent extends ApplicationEvent{ 
 public CustomApplicationEvent ( Object source, final String msg ){ 
 super(source); 
 System.out.println("Created a Custom event"); 
 } 
}
```

为了监听这个事件，还需要创建一个监听器：

```text
public class CustomEventListener implements ApplicationListener <
CustomApplicationEvent >{ 
 @Override 
 public void onApplicationEvent(CustomApplicationEvent
applicationEvent) { 
 //handle event 
 } 
}
```

之后通过 applicationContext 接口的 publishEvent\(\)方法来发布自定义事件。

```text
CustomApplicationEvent customEvent = new CustomApplicationEvent(applicationContext, "Test message"); 
applicationContext.publishEvent(customEvent);
```

#### 18.FileSystemResource 和 ClassPathResource 有何区别？

在 FileSystemResource 中需要给出 spring-config.xml 文件在你项目中的相对路径或者 绝对路径。在 ClassPathResource 中 spring 会在 ClassPath 中自动搜寻配置文件，所以要把 ClassPathResource 文件放在 ClassPath 下。 如果将 spring-config.xml 保存在了 src 文件夹下的话，只需给出配置文件的名称即可，因为 src 文件夹是默认。 简而言之，ClassPathResource 在环境变量中读取配置文件，FileSystemResource 在配置文件 中读取配置文件。

#### 19.IOC 容器对 Bean 的生命周期

1. 通过构造器或工厂方法创建 Bean 实例
2. 为 Bean 的属性设置值和对其他 Bean 的引用 
3. 将 Bean 实 例 传 递 给 Bean 前置 处 理 器 的 postProcessBeforeInitialization 方法
4. 调用 Bean 的初始化方法\(init-method\) 
5. 将 Bean 实 例 传 递 给 Bean 后 置 处 理 器 的 postProcessAfterInitialization 方法 
6. Bean 可以使用了 
7. 当容器关闭时, 调用 Bean 的销毁方法\(destroy-method\)

#### 20.@RestController vs ©Controller

Controller返回一个页面 单独使用©Controller不加@ResponseBody的话一般使用在要返回一个视图的情况，这种情况属于比 较传统的Spring MVC的应用，对应于前后端不分离的情况。

![](../../.gitbook/assets/image%20%289%29.png)

@RestController返回JSON或XML形式数据，但@RestController只返回对象，对象数据直接以JSON或XML形式写入HTTP响应\(Response\)中, 这种情况属于RESTfulWeb服务，这也是目前日常开发所接触的最常用的情况\(前后端分离\)。

![](../../.gitbook/assets/image%20%281%29.png)

©Controller +@ResponseBody 返回JSON 或 XML 形式数据

如 果 你 需 要 在 Spring4之 前 开 发 RESTful Web服 务 的 话 ，你 需 要 使 用 @ControUer并结 合gResponseBody注 解 ,也 就 是 说 ©Controller +@ResponseBodyJ \(aRestController \(Spring 4 之 后新加的注解\)。

> @ResponseBody注解的作用是将Controller的方法返回的对象通过适当的转换器转换为指定的格式之后，写入到HTTP响应\(Response\)对象的body中，通常用来返回JSON或者XML数据，返 回JSON数据的情况比较多。

#### 21.Spring IoC的初始化过程

![](../../.gitbook/assets/image%20%283%29.png)

IoC源码阅读

■ [https://javadoop.com/post/spring-ioc](https://javadoop.com/post/spring-ioc)

#### 22.@Component和 @Bean的区别是什么?

1. 作用对象不同:©Component注解作用于类，@Bean注解作用于方法。 2. 
2. @Component通常是通斗类路径扫描来自动侦测以及自动装配到Spring容 器 中 （我们可以使用 @ComponentScan注解定义要扫描的路径从中找出标识了需要装配的类自动装配到Spring的bean容 器中）。@Bean注解通常是我们在标有该注解的方法中定义产生这个bean,@Bean告诉了Spring这是 某个类的示例，当我需要用它的时候还给我。 
3.  @Bean注解比Component注解的自定义性更强，而且很多地方我们只能通过@Bean注解来注册 bean。比如当我们引用第三方库中的类需要装配到Spring容器时，则只能通过@Bean来实现。

#### 23.Spring框架中用到了哪些设计模式？

* 工厂设计模式:Spring使用工厂模式通过BeanFactory、A pplicationcontext创建bean对象。 
* 代理设计模式：Spring AOP功能的实现。 
* 单例设计模式：Spring中的Bean默认都是单例的。 
* 模板方法模式：Spring中 jdbcTemplate、hibernateTemplate等以Template结尾的对数据库操作 的类，它们就使用到了模板模式。
* 包装器设计模式：我们的项目需要连接多个数据库，而且不同的客户在每次访问中根据需要会去访问 不同的数据库。这种模式让我们可以根据客户的需求能够动态切换不同的数据源。 
* 观察者模式:Spring事件驱动模型就是观察者模式很经典的一个应用。 
* 适配器模式:Spring AOP的增强或通知（Advice）使用到了适配器模式、spring MVC中也是用到了适 配器模式适配C ontroller。





















