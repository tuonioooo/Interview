#### [【MQ】](/mq.md)

#### 参考地址

相关面试题参考地址：

[https://blog.csdn.net/zhengzhaoyang122/article/details/82184007](https://blog.csdn.net/zhengzhaoyang122/article/details/82184007)

[https://blog.csdn.net/songfeihu0810232/article/details/78648706](https://blog.csdn.net/songfeihu0810232/article/details/78648706)

作为中间件，消息队列是分布式应用间交换信息的重要组件。消息队列可驻留在内存或磁盘上, 队列可以存储消息直到它们被应用程序读走。通过消息队列，应用程序可以在不知道彼此位置的情况下独立处理消息，或者在处理消息前不需要等待接收此消息。所以消息队列可以解决应用解耦、异步消息、流量削锋等问题，是实现高性能、高可用、可伸缩和最终一致性架构中不可以或缺的一环。下面对消息队列就直接使用MQ表示。

![](/assets/message-queue.png)

现在比较常见的MQ产品主要是ActiveMQ、RabbitMQ、ZeroMQ、Kafka、MetaMQ、RocketMQ，当然还有很多其他的产品。因为个人水平有限，就简单的比较下这几种MQ的优缺点，作为自己选型的参考。

* # ZeroMQ {#1-zeromq}

![](/assets/logo.gif)



> ZeroMQ \(also known as ØMQ, 0MQ, or zmq\) looks like an embeddable networking library but acts like a concurrency framework. It gives you sockets that carry atomic messages across various transports like in-process, inter-process, TCP, and multicast. You can connect sockets N-to-N with patterns like fan-out, pub-sub, task distribution, and request-reply. It’s fast enough to be the fabric for clustered products. Its asynchronous I/O model gives you scalable multicore applications, built as asynchronous message-processing tasks. It has a score of language APIs and runs on most operating systems. ZeroMQ is from iMatix and is LGPLv3 open source.

ZeroMQ号称是“史上最快的消息队列”，基于c语言开发的，可以在任何平台通过任何代码连接，通过inproc、IPC、TCP、TIPC、多播传送消息，支持发布-订阅、推-拉、共享队列等模式，高速异步I/O引擎。



根据官方的说法，ZeroMQ是一个简单好用的传输层，像框架一样的可嵌入的socket类库，使Socket编程更加简单、简洁、性能更高，是专门为高吞吐量/低延迟的场景开发的。ZeroMQ与其他MQ有着本质的区别，它根本不是消息队列服务器，更类似与一个底层网络通讯库，对原有Socket API进行封装，在使用的使用引入对应的jar包即可，可谓是相当灵活。



同时，因为它的简单灵活，如果我们想作为消息队列使用的话，需要开发大量代码。而且，ZeroMQ不支持消息持久化，其定位并不是安全可靠的消息传输，所以还需要自己编码保证可靠性。简而言之一句话，ZeroMQ很强大，但是想用好需要自己实现。





