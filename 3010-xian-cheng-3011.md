### 参考地址：

相关面试总结：[https://www.cnblogs.com/tangjian07/p/10780692.html](https://www.cnblogs.com/tangjian07/p/10780692.html)

Java线程详解：[https://tuonioooo.gitbooks.io/java-concurrent/content/di-yi-7ae0-java-duo-xian-cheng-ji-chu.html](https://tuonioooo.gitbooks.io/java-concurrent/content/di-yi-7ae0-java-duo-xian-cheng-ji-chu.html)

**1.什么是线程？**

现代操作系统调度的最小单元是线程，也叫轻量级进程（LightWeight Process），在一个进程里可以创建多个线程，这些线程都拥有各自的计数器、堆栈和局部变量等属性，并且能够访问共享的内存变量。处理器在这些线程上高速切换，让使用者感觉到这些线程在同时执行

**2.为什么要使用多线程/线程使用的好处？              
**（1）更多的处理器核心

（2）更快的响应时间

（3）更好的编程模型

**3.线程的状态？**

初始状态、运行状态、阻塞状态、等待状态、超时等待状态、终止状态** **

**4.启动线程、线程中断？              
**start\(\)方法启动线程、interrupt\(\)

**5.如何安全地终止线程？              
**在中断线程节中提到的中断状态是线程的一个标识位，而中断操作是一种简便的线程间交互方式，而这种交互方式最适合用来取消或停止任务。除了中断以外，还可以利用一个boolean变量来控制是否需要停止任务并终止该线程。在代码清单1所示的例子中，创建了一个线程CountThread，它不断地进行变量累加，而主线程尝试对其进行中断操作和停止操作

```
package com.ise.api.thread;

import java.util.concurrent.TimeUnit;

public class Shutdown {
    public static void main(String[] args) throws Exception {
        Runner one = new Runner();
        Thread countThread = new Thread(one, "CountThread");
        countThread.start();
        // 睡眠1秒，main线程对CountThread进行中断，使CountThread能够感知中断而结束
        TimeUnit.SECONDS.sleep(1);
        countThread.interrupt();
        Runner two = new Runner();
        countThread = new Thread(two, "CountThread");
        countThread.start();
        // 睡眠1秒，main线程对Runner two进行取消，使CountThread能够感知on为false而结束
        TimeUnit.SECONDS.sleep(1);
        two.cancel();
    }

    private static class Runner implements Runnable {
        private long i;
        private volatile boolean on = true;

        @Override
        public void run() {
            while (on && !Thread.currentThread().isInterrupted()) {
                i++;
            }
            System.out.println("Count i = " + i);
        }

        public void cancel() {
            on = false;
        }
    }
}
```

**6.多线程的实现方法？              
**继承 Thread 类、实现 Runnable 接口，在程序开发中只要是多线程，肯定永远以实现 Runnable 接口为主，因为实现 Runnable 接口相比继承 Thread 类有如下优势：

* 可以避免由于 Java 的单继承特性而带来的局限；
* 增强程序的健壮性，代码能够被多个线程共享，代码与数据是独立的；
* 适合多个相同程序代码的线程区处理同一资源的情况。

**7.多线程环境下，局部变量和全局变量都会共享吗？**

局部变量不会受多线程影响

成员变量会受到多线程影响

多个线程调用的同一个对象的同一个方法：

* 如果方法里无成员变量，不受任何影响
* 如果方法里有成员变量，只有读操作，不受影响，存在赋值操作，有影响



