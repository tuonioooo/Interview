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

**8.volatile、ThreadLocal、synchronized等3个关键字区别？**

* **volatile**

volatile主要是用来在多线程中同步变量。在一般情况下，为了提升性能，每个线程在运行时都会将主内存中的变量保存一份在自己的内存中作为变量副本，但是这样就很容易出现多个线程中保存的副本变量不一致，或与主内存的中的变量值不一致的情况。而当一个变量被volatile修饰后，该变量就不能被缓存到线程的内存中，它会告诉编译器不要进行任何移出读取和写入操作的优化，换句话说就是不允许有不同于“主”内存区域的变量拷贝，所以当该变量有变化时，所有调用该变量的线程都会获得相同的值，这就确保了该变量在应用中的可视性\(当一个任务做出了修改在应用中必须是可视的\)，同时性能也相应的降低了\(还是比synchronized高\)。但需要注意volatile只能确保操作的是同一块内存，并不能保证操作的原子性。所以volatile一般用于声明简单类型变量，使得这些变量具有原子性，即一些简单的赋值与返回操作将被确保不中断。但是当该变量的值由自身的上一个决定时，volatile的作用就将失效，这是由volatile关键字的性质所决定的。所以在volatile时一定要谨慎，千万不要以为用volatile修饰后该变量的所有操作都是原子操作，不再需要synchronized关键字了。+

* **ThreadLocal**

首先ThreadLocal和本地线程没有一毛钱关系，更不是一个特殊的Thread，它只是一个线程的局部变量\(其实就是一个Map\),ThreadLocal会为每个使用该变量的线程提供独立的变量副本，所以每一个线程都可以独立地改变自己的副本，而不会影响其它线程所对应的副本。这样做其实就是以空间换时间的方式\(与synchronized相反\)，以耗费内存为代价，单大大减少了线程同步\(如synchronized\)所带来性能消耗以及减少了线程并发控制的复杂度。但需要注意的是，虽然ThreadLocal和Synchonized都用于解决多线程并发访问，ThreadLocal与synchronized还是有本质的区别。synchronized是利用锁的机制，使变量或代码块在某一时该只能被一个线程访问。而ThreadLocal为每一个线程都提供了变量的副本，使得每个线程在某一时间访问到的并不是同一个对象，这样就隔离了多个线程对数据的数据共享。而Synchronized却正好相反，它用于在多个线程间通信时能够获得数据共享。即Synchronized用于线程间的数据共享，而ThreadLocal则用于线程间的数据隔离。所以ThreadLocal并不能代替synchronized，Synchronized的功能范围更广\(同步机制\)。

* **synchronized**

synchronized关键字是[Java](http://lib.csdn.net/base/17)利用锁的机制自动实现的，一般有同步方法和同步代码块两种使用方式。Java中所有的对象都自动含有单一的锁\(也称为监视器\)，当在对象上调用其任意的synchronized方法时，此对象被加锁\(一个任务可以多次获得对象的锁，计数会递增\)，同时在线程从该方法返回之前，该对象内其他所有要调用类中被标记为synchronized的方法的线程都会被阻塞。当然针对每个类也有一个锁\(作为类的Class对象的一部分\)，所以你懂的^.^。最后需要注意的是synchronized是同步机制中最安全的一种方式，其他的任何方式都是有风险的，当然付出的代价也是最大的

**9.Java线程等待和通知的相关方法?**

**10.什么是连接池、线程池，各种的优点？**

**11.常用的几种线程池创建方法？**

**12.Future、FutureTask、CompletionService、CompletableFuture区别?**

**13.HashMap实现原理?**

**14.为什么要使用ConcurrentHashMap?**











