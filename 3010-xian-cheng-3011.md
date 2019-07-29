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

**8.volatile、ThreadLocal、synchronized等3个关键字区别？**

* **volatile**

volatile主要是用来在多线程中同步变量。在一般情况下，为了提升性能，每个线程在运行时都会将主内存中的变量保存一份在自己的内存中作为变量副本，但是这样就很容易出现多个线程中保存的副本变量不一致，或与主内存的中的变量值不一致的情况。而当一个变量被volatile修饰后，该变量就不能被缓存到线程的内存中，它会告诉编译器不要进行任何移出读取和写入操作的优化，换句话说就是不允许有不同于“主”内存区域的变量拷贝，所以当该变量有变化时，所有调用该变量的线程都会获得相同的值，这就确保了该变量在应用中的可视性\(当一个任务做出了修改在应用中必须是可视的\)，同时性能也相应的降低了\(还是比synchronized高\)。但需要注意volatile只能确保操作的是同一块内存，并不能保证操作的原子性。所以volatile一般用于声明简单类型变量，使得这些变量具有原子性，即一些简单的赋值与返回操作将被确保不中断。但是当该变量的值由自身的上一个决定时，volatile的作用就将失效，这是由volatile关键字的性质所决定的。所以在volatile时一定要谨慎，千万不要以为用volatile修饰后该变量的所有操作都是原子操作，不再需要synchronized关键字了。+

* **ThreadLocal**

首先ThreadLocal和本地线程没有一毛钱关系，更不是一个特殊的Thread，它只是一个线程的局部变量\(其实就是一个Map\),ThreadLocal会为每个使用该变量的线程提供独立的变量副本，所以每一个线程都可以独立地改变自己的副本，而不会影响其它线程所对应的副本。这样做其实就是以空间换时间的方式\(与synchronized相反\)，以耗费内存为代价，单大大减少了线程同步\(如synchronized\)所带来性能消耗以及减少了线程并发控制的复杂度。但需要注意的是，虽然ThreadLocal和Synchonized都用于解决多线程并发访问，ThreadLocal与synchronized还是有本质的区别。synchronized是利用锁的机制，使变量或代码块在某一时该只能被一个线程访问。而ThreadLocal为每一个线程都提供了变量的副本，使得每个线程在某一时间访问到的并不是同一个对象，这样就隔离了多个线程对数据的数据共享。而Synchronized却正好相反，它用于在多个线程间通信时能够获得数据共享。即Synchronized用于线程间的数据共享，而ThreadLocal则用于线程间的数据隔离。所以ThreadLocal并不能代替synchronized，Synchronized的功能范围更广\(同步机制\)。

* **synchronized**

synchronized关键字是[Java](http://lib.csdn.net/base/17)利用锁的机制自动实现的，一般有同步方法和同步代码块两种使用方式。Java中所有的对象都自动含有单一的锁\(也称为监视器\)，当在对象上调用其任意的synchronized方法时，此对象被加锁\(一个任务可以多次获得对象的锁，计数会递增\)，同时在线程从该方法返回之前，该对象内其他所有要调用类中被标记为synchronized的方法的线程都会被阻塞。当然针对每个类也有一个锁\(作为类的Class对象的一部分\)，所以你懂的^.^。最后需要注意的是synchronized是同步机制中最安全的一种方式，其他的任何方式都是有风险的，当然付出的代价也是最大的

**9.Java线程等待和通知的相关方法?              
**![](/assets/import-4-2.png)

**10.什么是连接池、线程池，各自的优点？**

* [**连接池**](https://tuonioooo.gitbooks.io/java-concurrent/content/di-yi-7ae0-java-duo-xian-cheng-ji-chu/shi-zhan-ying-yong/lian-jie-chi.html)

* [**线程池**](https://tuonioooo.gitbooks.io/java-concurrent/content/di-yi-7ae0-java-duo-xian-cheng-ji-chu/shi-zhan-ying-yong/xian-cheng-chi.html)

**11.常用的几种线程池创建方法？**

* **newCachedThreadPool\(\)**

缓存型池子，先查看池中有没有以前建立的线程，如果有，就 reuse 如果没有，就建一个新的线程加入池中

缓存型池子通常用于执行一些生存期很短的异步型任务 因此在一些面向连接的 daemon 型 SERVER 中用得不多。但对于生存期短的异步任务，它是 Executor 的首选。

能 reuse 的线程，必须是 timeout IDLE 内的池中线程，缺省 timeout 是 60s,超过这个 IDLE 时长，线程实例将被终止及移出池。

> 注意，放入 CachedThreadPool 的线程不必担心其结束，超过 TIMEOUT 不活动，其会自动被终止。
>
> 无界线程池，可以进行自动线程回收

* **newFixedThreadPool\(int\)**

newFixedThreadPool 与 cacheThreadPool 差不多，也是能 reuse 就用，但不能随时建新的线程。

其独特之处:任意时间点，最多只能有固定数目的活动线程存在，此时如果有新的线程要建立，只能放在另外的队列中等待，直到当前的线程中某个线程终止直接被移出池子。和 cacheThreadPool 不同，FixedThreadPool 没有 IDLE 机制（可能也有，但既然文档没提，肯定非常长，类似依赖上层的 TCP 或 UDP IDLE 机制之类的），所以 FixedThreadPool 多数针对一些很稳定很固定的正规并发线程，多用于服务器。

从方法的源代码看，cache池和fixed 池调用的是同一个底层 池，只不过参数不同:

* fixed 池线程数固定，并且是0秒IDLE（无IDLE）。
* cache 池线程数支持 0-Integer.MAX\_VALUE\(显然完全没考虑主机的资源承受能力），60 秒 IDLE 。

> **注意**：固定大小线程池

* **newScheduledThreadPool\(int\)**

调度型线程池，这个池子里的线程可以按 schedule 依次 delay 执行，或周期执行

* **newSingleThreadExecutor\(\)**

单例线程，任意时间池中只能有一个线程，用的是和 cache 池和 fixed 池相同的底层池，但线程数目是 1-1,0 秒 IDLE（无 IDLE）

一般来说，CachedTheadPool 在程序执行过程中通常会创建与所需数量相同的线程，然后在它回收旧线程时停止创建新线程，因此它是合理的 Executor 的首选，只有当这种方式会引发问题时（比如需要大量长时间面向连接的线程时），才需要考虑用 FixedThreadPool。（该段话摘自《Thinking in Java》第四版）+

**12.Future、FutureTask、CompletionService、CompletableFuture区别?              
**[https://www.cnblogs.com/dennyzhangdd/p/7010972.html](https://www.cnblogs.com/dennyzhangdd/p/7010972.html)

**13.HashMap实现原理?       
**[  **hashmap实现原理详解**](https://tuonioooo.gitbooks.io/java-concurrent/content/hashmapshi-xian-yuan-li.html)

**14.为什么要使用ConcurrentHashMap?**

[**参考答案**](https://tuonioooo.gitbooks.io/java-concurrent/content/concurrenthashmapde-shi-xian-yuan-li-yu-shi-yong/wei-shi-yao-yao-shi-yong-concurrenthashmap.html)

**15.你对线程优先级的理解是什么？**

每一个线程都是有优先级的，一般来说，高优先级的线程在运行时会具有优先权，但这依赖于线程调度的实现，这个实现是和操作系统相关的\(OS dependent\)。我们可以定义线程的优先级，但是这并不能保证高优先级的线程会在低优先级的线程前执行。线程优先级是一个int变量\(从1-10\)，1代表最低优先级，10代表最高优先级。

**16.什么是线程安全？Vector是一个线程安全类吗？**

如果你的代码所在的进程中有多个线程在同时运行，而这些线程可能会同时运行这段代码。如果每次运行结果和单线程运行的结果是一样的，而且其他的变量的值也和预期的是一样的，就是线程安全的。一个线程安全的计数器类的同一个实例对象在被多个线程使用的情况下也不会出现计算失误。很显然你可以将集合类分成两组，线程安全和非线程安全的。Vector 是用同步方法来实现线程安全的, 而和它相似的ArrayList不是线程安全的。





