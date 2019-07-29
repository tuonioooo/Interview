* ### 参考地址：

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

![](/assets/1588033-20190428221654326-894392129.png)

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

**6.创建线程有哪几种方式？                                                               
**创建线程有三种方式：

* * 继承
    Thread 重写 run 方法；
  * 实现
    Runnable 接口；
  * 实现
    Callable 接口。

  > 补充：说  
  > 一下 runnable 和 callable 有什么区别？  
  > runnable 没有返回值，callable 可以拿到有返回值，callable 可以看作是 runnable 的补充。

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

**17.Java中如何停止一个线程？**

Java提供了很丰富的API但没有为停止线程提供API。JDK 1.0本来有一些像stop\(\), suspend\(\) 和 resume\(\)的控制方法但是由于潜在的死锁威胁因此在后续的JDK版本中他们被弃用了，之后Java API的设计者就没有提供一个兼容且线程安全的方法来停止一个线程。当run\(\) 或者 call\(\) 方法执行完的时候线程会自动结束,如果要手动结束一个线程，你可以用volatile 布尔变量来退出run\(\)方法的循环或者是取消任务来中断线程

**18.什么是线程饿死，什么是活锁？**

当所有线程阻塞，或者由于需要的资源无效而不能处理，不存在非阻塞线程使资源可用。JavaAPI中线程活锁可能发生在以下情形：

1，当所有线程在程序中执行Object.wait\(0\)，参数为0的wait方法。程序将发生活锁直到在相应的对象上有线程调用Object.notify\(\)或者Object.notifyAll\(\)。

2，当所有线程卡在无限循环中。

**19.什么是Java Timer类？如何创建一个有特定时间间隔的任务？**

java.util.Timer是一个工具类，可以用于安排一个线程在未来的某个特定时间执行。Timer类可以用安排一次性任务或者周期任务。

java.util.TimerTask是一个实现了Runnable接口的抽象类，我们需要去继承这个类来创建我们自己的定时任务并使用Timer去安排它的执行

**20.Java中的同步集合与并发集合有什么区别？**

同步集合与并发集合都为多线程和并发提供了合适的线程安全的集合，不过并发集合的可扩展性更高。在Java1.5之前程序员们只有同步集合来用且在多线程并发的时候会导致争用，阻碍了系统的扩展性。Java5介绍了并发集合像ConcurrentHashMap，不仅提供线程安全还用锁分离和内部分区等现代技术提高了可扩展性。

不管是同步集合还是并发集合他们都支持线程安全，他们之间主要的区别体现在性能和可扩展性，还有他们如何实现的线程安全上。

同步HashMap, Hashtable, HashSet, Vector, ArrayList 相比他们并发的实现（ConcurrentHashMap, CopyOnWriteArrayList, CopyOnWriteHashSet）会慢得多。造成如此慢的主要原因是锁， 同步集合会把整个Map或List锁起来，而并发集合不会。并发集合实现线程安全是通过使用先进的和成熟的技术像锁剥离。

比如ConcurrentHashMap 会把整个Map 划分成几个片段，只对相关的几个片段上锁，同时允许多线程访问其他未上锁的片段。

同样的，CopyOnWriteArrayList 允许多个线程以非同步的方式读，当有线程写的时候它会将整个List复制一个副本给它。

如果在读多写少这种对并发集合有利的条件下使用并发集合，这会比使用同步集合更具有可伸缩性。

**21.同步方法和同步块，哪个是更好的选择？**

同步块是更好的选择，因为它不会锁住整个对象（当然你也可以让它锁住整个对象）。同步方法会锁住整个对象，哪怕这个类中有多个不相关联的同步块，这通常会导致他们停止执行并需要等待获得这个对象上的锁。

**22.Java中invokeAndWait 和 invokeLater有什么区别？**

这两个方法是Swing API 提供给Java开发者用来从当前线程而不是事件派发线程更新GUI组件用的。InvokeAndWait\(\)同步更新GUI组件，比如一个进度条，一旦进度更新了，进度条也要做出相应改变。如果进度被多个线程跟踪，那么就调用invokeAndWait\(\)方法请求事件派发线程对组件进行相应更新。而invokeLater\(\)方法是异步调用更新组件的。

**23.多线程中的忙循环是什么?**

忙循环就是程序员用循环让一个线程等待，不像传统方法wait\(\), sleep\(\) 或 yield\(\) 它们都放弃了CPU控制，而忙循环不会放弃CPU，它就是在运行一个空循环。这么做的目的是为了保留CPU缓存。

在多核系统中，一个等待线程醒来的时候可能会在另一个内核运行，这样会重建缓存。为了避免重建缓存和减少等待重建的时间就可以使用它了。

**24.Java中interrupted 和isInterruptedd方法的区别？**

interrupted\(\) 和 isInterrupted\(\)的主要区别是前者会将中断状态清除而后者不会。Java多线程的中断机制是用内部标识来实现的，调用Thread.interrupt\(\)来中断一个线程就会设置中断标识为true。当中断线程调用静态方法Thread.interrupted\(\)来检查中断状态时，中断状态会被清零。

非静态方法isInterrupted\(\)用来查询其它线程的中断状态且不会改变中断状态标识。简单的说就是任何抛出InterruptedException异常的方法都会将中断状态清零。无论如何，一个线程的中断状态都有可能被其它线程调用中断来改变。

**25.Java中活锁和死锁有什么区别？**

活锁：一个线程通常会有会响应其他线程的活动。如果其他线程也会响应另一个线程的活动，那么就有可能发生活锁。同死锁一样，发生活锁的线程无法继续执行。然而线程并没有阻塞——他们在忙于响应对方无法恢复工作。这就相当于两个在走廊相遇的人：甲向他自己的左边靠想让乙过去，而乙向他的右边靠想让甲过去。可见他们阻塞了对方。甲向他的右边靠，而乙向他的左边靠，他们还是阻塞了对方。

死锁：两个或更多线程阻塞着等待其它处于死锁状态的线程所持有的锁。死锁通常发生在多个线程同时但以不同的顺序请求同一组锁的时候，死锁会让你的程序挂起无法完成任务。

**26.如何避免死锁？**

死锁的发生必须满足以下四个条件：

* 互斥条件：一个资源每次只能被一个进程使用。
* 请求与保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放。
* 不剥夺条件：进程已获得的资源，在末使用完之前，不能强行剥夺。
* 循环等待条件：若干进程之间形成一种头尾相接的循环等待资源关系。

两种种用于避免死锁的技术：

* 加锁顺序（线程按照一定的顺序加锁）
* 加锁时限（线程尝试获取锁的时候加上一定的时限，超过时限则放弃对该锁的请求，并释放自己占有的锁）

**27.notify\(\)和notifyAll\(\)有什么区别？**

* notify\(\)和notifyAll\(\)都是Object对象用于通知处在等待该对象的线程的方法。

* void notify\(\): 唤醒一个正在等待该对象的线程。

* void notifyAll\(\): 唤醒所有正在等待该对象的线程。

  两者的最大区别在于：

  notifyAll使所有原来在该对象上等待被notify的线程统统退出wait的状态，变成等待该对象上的锁，一旦该对象被解锁，他们就会去竞争。

  notify他只是选择一个wait状态线程进行通知，并使它获得该对象上的锁，但不惊动其他同样在等待被该对象notify的线程们，当第一个线程运行完毕以后释放对象上的锁，此时如果该对象没有再次使用notify语句，即便该对象已经空闲，其他wait状态等待的线程由于没有得到该对象的通知，继续处在wait状态，直到这个对象发出一个notify或notifyAll，它们等待的是被notify或notifyAll，而不是锁。

**28.什么是可重入锁（ReentrantLock）？**

Java.util.concurrent.lock 中的 Lock 框架是锁定的一个抽象，它允许把锁定的实现作为Java 类，而不是作为语言的特性来实现。这就为Lock 的多种实现留下了空间，各种实现可能有不同的调度算法、性能特性或者锁定语义。 ReentrantLock 类实现了Lock ，它拥有与synchronized 相同的并发性和内存语义，但是添加了类似锁投票、定时锁等候和可中断锁等候的一些特性。此外，它还提供了在激烈争用情况下更佳的性能。（换句话说，当许多线程都想访问共享资源时，JVM可以花更少的时候来调度线程，把更多时间用在执行线程上。）

Reentrant 锁意味着什么呢？简单来说，它有一个与锁相关的获取计数器，如果拥有锁的某个线程再次得到锁，那么获取计数器就加1，然后锁需要被释放两次才能获得真正释放。这模仿了synchronized 的语义；如果线程进入由线程已经拥有的监控器保护的synchronized 块，就允许线程继续进行，当线程退出第二个（或者后续）synchronized块的时候，不释放锁，只有线程退出它进入的监控器保护的第一个synchronized 块时，才释放锁

**29.读写锁可以用于什么应用场景？**

读写锁可以用于 “多读少写” 的场景，读写锁支持多个读操作并发执行，写操作只能由一个线程来操作

ReadWriteLock对向数据结构相对不频繁地写入，但是有多个任务要经常读取这个数据结构的这类情况进行了优化。ReadWriteLock使得你可以同时有多个读取者，只要它们都不试图写入即可。如果写锁已经被其他任务持有，那么任何读取者都不能访问，直至这个写锁被释放为止。

ReadWriteLock 对程序性能的提高主要受制于如下几个因素：

1，数据被读取的频率与被修改的频率相比较的结果。

2，读取和写入的时间

3，有多少线程竞争

4，是否在多处理机器上运行

**30.守护线程是什么？**

守护线程是运行在后台的一种特殊进程。它独立于控制终端并且周期性地执行某种任务或等待处理某些发生的事件。在Java 中垃圾回收线程就是特殊的守护线程。

**31.并行和并发有什么区别？**

* 并行：多个处理器或多核处理器同时处理多个任务。（是真正的物理上的同时发生）
* 并发：多个任务在同一个
  CPU 核上，按细分的时间片轮流\(交替\)执行，从逻辑上来看那些任务是同时执行。（逻辑上的同时发生）

举个例子：排队打饭，当只有一个窗口可以打饭的时候，我们排成了两排，那么这两排只能一边一个来，这就是并发；  
　　　　　　　　　　　　如果有两个窗口可以打饭的话，一个队一个窗口，那么这两排可以同时进行打饭，这就是并行了。

**32.sleep\(\) 和 wait\(\) 有什么区别？**

* 类的不同：
  sleep\(\) 来自 Thread，wait\(\) 来自 Object。
* 释放锁：
  sleep\(\) 不释放锁；wait\(\) 释放锁。
* 用法不同：
  sleep\(\) 时间到会自动恢复；wait\(\) 可以使用 notify\(\)/notifyAll\(\)直接唤醒。

**33.线程的 run\(\) 和 start\(\) 有什么区别？**

start\(\) 方法用于启动线程，run\(\) 方法用于执行线程的运行时代码。run\(\) 可以重复调用，而 start\(\) 只能调用一次。

**34**.**创建线程池有哪几种方式？**

线程池创建有七种方式，最核心的是最后一种：

* newSingleThreadExecutor\(\)：它的特点在于工作线程数目被限制为 1，操作一个无界的工作队列，所以它保证了所有任务的都是被顺序执行，最多会有一个任务处于活动状态，并且不允许使用者改动线程池实例，因此可以避免其改变线程数目；

* newCachedThreadPool\(\)：它是一种用来处理大量短时间工作任务的线程池，具有几个鲜明特点：它会试图缓存线程并重用，当无缓存线程可用时，就会创建新的工作线程；如果线程闲置的时间超过 60 秒，则被终止并移出缓存；长时间闲置时，这种线程池，不会消耗什么资源。其内部使用 SynchronousQueue 作为工作队列；

* newFixedThreadPool\(int nThreads\)：重用指定数目（nThreads）的线程，其背后使用的是无界的工作队列，任何时候最多有 nThreads 个工作线程是活动的。这意味着，如果任务数量超过了活动队列数目，将在工作队列中等待空闲线程出现；如果有工作线程退出，将会有新的工作线程被创建，以补足指定的数目 nThreads；

* newSingleThreadScheduledExecutor\(\)：创建单线程池，返回 ScheduledExecutorService，可以进行定时或周期性的工作调度；

* newScheduledThreadPool\(int corePoolSize\)：和newSingleThreadScheduledExecutor\(\)类似，创建的是个 ScheduledExecutorService，可以进行定时或周期性的工作调度，区别在于单一工作线程还是多个工作线程；

* newWorkStealingPool\(int parallelism\)：这是一个经常被人忽略的线程池，Java 8 才加入这个创建方法，其内部会构建ForkJoinPool，利用Work-Stealing算法，并行地处理任务，不保证处理顺序；

* ThreadPoolExecutor\(\)：是最原始的线程池创建，上面1-3创建方式都是对ThreadPoolExecutor的封装。

**35.线程池都有哪些状态？**

* RUNNING：这是最正常的状态，接受新的任务，处理等待队列中的任务。
* SHUTDOWN：不接受新的任务提交，但是会继续处理等待队列中的任务。
* STOP：不接受新的任务提交，不再处理等待队列中的任务，中断正在执行任务的线程。
* TIDYING：所有的任务都销毁了，workCount 为 0，线程池的状态在转换为 TIDYING 状态时，会执行钩子方法 terminated\(\)。
* TERMINATED：terminated\(\)方法结束后，线程池的状态就会变成这个。

**36.线程池中 submit\(\) 和 execute\(\) 方法有什么区别？**

* execute\(\)：只能执行 Runnable 类型的任务。

* submit\(\)：可以执行 Runnable 和 Callable 类型的任务。

Callable 类型的任务可以获取执行的返回值，而 Runnable 执行无返回值。

**37.在 Java 程序中怎么保证多线程的运行安全？**

* 方法一：使用安全类，比如Java. util. concurrent 下的类。
* 方法二：使用自动锁synchronized。
* 方法三：使用手动锁Lock。



