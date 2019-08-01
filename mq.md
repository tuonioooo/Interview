#### [【MQ】](/mq.md)

#### 参考地址

相关面试题参考地址：

[https://blog.csdn.net/zhengzhaoyang122/article/details/82184007](https://blog.csdn.net/zhengzhaoyang122/article/details/82184007)

[https://blog.csdn.net/songfeihu0810232/article/details/78648706](https://blog.csdn.net/songfeihu0810232/article/details/78648706)

### JMS、ActiveMQ和RabbitMQ的区别与联系

**JMS：**

Java Message Service，Java消息服务应用程序接口，Java中定义的消息中间件服务的一个标准和API定义

JMS是Java平台上有关面向消息中间件（MOM）的技术规范，它便于消息系统中的Java应用程序进行消息交换，并且通过提供标准的产生、发送、接收消息的接口简化企业应用的开发。

**MQ：**

Message Queue，消息队列，是一种应用程序对应用程序的通信方法。

MQ是遵循AMQP协议的具体实现和产品。

生产者模型则是MQ的一个典型的代表，一端往消息队列中不断的写入消息，而另一端则可以读取或者订阅队列中的消息。

**JMS和MQ的关系：**

JMS是一个用于提供消息服务的技术规范，它制定了在整个消息服务提供过程中的所有数据结构和交互流程。而MQ则是消息队列服务，是面向消息中间件（MOM）的最终实现，是真正的服务提供者，MQ的实现可以基于JMS，也可以基于其他规范或标准。

