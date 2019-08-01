#### [【MQ】](/mq.md)

#### 参考地址

相关面试题参考地址：

[https://blog.csdn.net/zhengzhaoyang122/article/details/82184007](https://blog.csdn.net/zhengzhaoyang122/article/details/82184007)

[https://blog.csdn.net/songfeihu0810232/article/details/78648706](https://blog.csdn.net/songfeihu0810232/article/details/78648706)



作为中间件，消息队列是分布式应用间交换信息的重要组件。消息队列可驻留在内存或磁盘上, 队列可以存储消息直到它们被应用程序读走。通过消息队列，应用程序可以在不知道彼此位置的情况下独立处理消息，或者在处理消息前不需要等待接收此消息。所以消息队列可以解决应用解耦、异步消息、流量削锋等问题，是实现高性能、高可用、可伸缩和最终一致性架构中不可以或缺的一环。下面对消息队列就直接使用MQ表示。

![](/assets/message-queue.png)

现在比较常见的MQ产品主要是ActiveMQ、RabbitMQ、ZeroMQ、Kafka、MetaMQ、RocketMQ，当然还有很多其他的产品。因为个人水平有限，就简单的比较下这几种MQ的优缺点，作为自己选型的参考。



