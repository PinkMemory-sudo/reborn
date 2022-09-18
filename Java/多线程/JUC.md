* workQueue

* ThreadLoacl

* 线程调度

java.util.concurrent

JDK5提供的并发编程工具包

包含三部分：

java.util.concurrent

java.util.concurrent.automic

java.util.concurrent.locks



# 创建线程的四种方式

1. 继承Thread，子类重写run方法，创建子类对象
2. 实现Runnable接口，用过Thread对象执行Runnable的run方法
3. 实现Callable接口的call方法，Callable对象传入FutureTask，可以通过Thread或者线程池执行线程



## Thread



## Runnable



## Callable

​		有返回结果的线程任务，结果必须存储在主线程已知的对象中，以便主线程可以知道该线程返回的结果

Future视为保存结果的对象。

​		函数式借接口，call方法指定任务



**Callable的不同**

* Callable有返回值
* Callable可以抛异常
* 运行Callable任务可以拿到一个Future对象，表示异步计算的结果



Callable，Future，FutureTask

Callable与Runnable的区别



### 执行Callable的两种方式



1. **借助FutureTask**

FutureTask实现类Runnable，所以可以当成线程任务传入Thread

FutureTask实现了Future，所以可以获得线程的执行结果，(通过调用FutureTask的get方法)

```java
CallableTest task = new CallableTest();
FutureTask<String> futureTask = new FutureTask<>(task);
new Thread(futureTask).start();
System.out.println(futureTask.get());
```

2. 借助线程池，将Callable提交到线程池中

```java
ExecutorService threadPool = Executors.newSingleThreadExecutor();
Future<?> future = threadPool.submit(new CallableTest());
try {
  TimeUnit.SECONDS.sleep(5);
  System.out.println(future.get());
} catch (InterruptedException | ExecutionException e) {
  e.printStackTrace();
}
```



**Runnable转Callable**

用工厂类Executors来把一个Runnable对象包装成Callable对象

```java
public static Callable<Object> callable(Runnable task)
public static <T> Callable<T> callable(Runnable task, T result)
```



### Future接口

**用来获得线程的执行结果**，取消线程等

`boolean cancel(boolean mayInterruptIfRunning)`

任务还没有执行是会返回false

任务已经启动后，将以中断执行此任务线程的方式来试图停止任务，停止成功返回true

任务正在运行时不会取消，但会false

任务执行完后执行方法会返回false



| 方法                               | 表述                                                    |
| ---------------------------------- | ------------------------------------------------------- |
|                                    |                                                         |
| boolean isCancelled();             |                                                         |
| boolean isDone();                  | 执行完成还是取消，异常都是true，即只有正在执行才是false |
| V get()                            | 此方法会阻塞直到异步计算完成                            |
| V get(long timeout, TimeUnit unit) | 阻塞超出时间将抛异常                                    |



### FutureTask

实现了Runnable和Future，所以他可以当成任务给线程，然后可以通过他获得线程的执行结果。

**构造方法**

```java

public FutureTask(Callable<V> callable)
public FutureTask(Runnable runnable, V result)

```





进程与线程的区别

进程是操作系统资源分配的基本单位

一个线程中可以"同时"执行多个任务



并发与并行

并发就是多个线程同时争取一个资源，并行不争夺资源同时进行

高内聚和底耦合的前提下，线程 任务 资源类：线程通过任务去操作资源类，资源类对外暴露方法供线程调用。

资源与操作高内聚，资源与线程低耦合。

数据共享但各自独立



一般不用synchronized了，使用JUC中的原子性操作，lock等

需要同步时lock，不需要时(try-finally)unlock



ReentrantLock

可重入锁



多线程之间的通信：执行的任务不同但是资源相同













```
Exception in thread "produce" Exception in thread "consum" java.lang.IllegalMonitorStateException
	at java.lang.Object.notify(Native Method)
```

在调用wait/notify时要同步



## Volatile

共享数据添加volatile关键字，保证内存可见性，相较于 synchronized 是一种较为轻量级的同步策略;

**volatile和atmic是轻量级的同步**使用不好还是会出现线程安全问题



**volatile保证内存可见性但是不保证原子性和互斥性**



内存可见性：保证读的时候是正确的



volatile修饰的变量，可以满足可见性，但是进行自增等复合操作时不能保证原子性。





内存可见性：

每个线程将共享数据读取到，一个线程改变了共享变量时，另一个线程不知道

使用volatile前

```java
package com.pk.threadtest.thread.volatiletest;

import java.util.concurrent.TimeUnit;

public class NoVolatile {
    public static void main(String[] args) {
        DemonSource demonSource = new DemonSource();
        new Thread(demonSource::changeFlag).start();
        // 可以看出主线程结束不了，因为主线程读到的flag是false，另一个线程改变了flag时，主线程感知不到
        // 解决方法: 共享变量flag前加volatile
        while (true) {
            boolean flag = demonSource.getFlag();
            if (flag) {
                System.out.println("hello");
                break;
            }
        }
    }
}

class DemonSource {
    private boolean flag = false;

    public boolean getFlag() {
        return flag;
    }

    public void changeFlag() {
        try {
            // 改变状态操作需要用时1s(主线程读到改变前的flag)
            TimeUnit.SECONDS.sleep(1);
            flag = !flag;
            System.out.println("changeFlagSuccess");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

**当多个线程同时操作共享变量时，彼此是不可见的**

所以在改变flag前，主线程将flag读入，改变flag后，主线程中的flag不变



**原子性问题**
++操作实际包含了三步操作：读、写、改

JUC的atomic包中，提供了常用的原子变量

volatile来保证内存可见性

CAS算法来保证原子性



原子性与线程安全：

原子性！=线程安全

原子性保证了--的操作不会被打断，但是仍然有可能多个线程都进入了判断条件执行--操作，导致小于0的出现，

非原子性操作





# 并发容器类



## ConcurrentHashMap



# 同步锁



# 线程调度







# Locks



## Condition接口



await

```
Causes the current thread to wait until it is signalled or thread interrupt.
```



## Lock

相比于Synchronized，有灵活的结构属性，支持多条件(Condition)







## ReentrantLock





## ReadWriteLock

同一时刻只有获得锁的线程才能操作共享资源。

但是，一些锁可以允许并发访问共享资源，如ReadWriteLock







## StampedLock



一个资源，多种操作，多个线程



# 线程间的通信

线程调度

生产者消费者模式：**判断->操作->通知**

判断是否需要操作，不需要时放弃执行权(**确定线程执行的顺序**)

操作资源，操作完后通知其他线程

**资源通常包括什么？**

标志位



**操作资源？**

如果线程间有调度的话要判断



`两个线程操作初始值为0的变量，使变量0101交替执行10次`

首先确定资源和操作，资源是一个变量，操作是对变量进行加减。所以资源类中包含两个操作：加和减

接着是线程的操作：一个线程去加，一个线程与减，加减操作交替执行，两个操作就要使用的就是生产者消费者模型。

资源类封装**基本操作**供线程实用

```java
public class MutilPCResource {
    private int num = 0;

    public synchronized void add() throws InterruptedException {
        if (num > 0) {
            this.wait();
        }
        num++;
        System.out.println(Thread.currentThread().getName() + "__" + num);
        this.notify();
    }

    public synchronized void minus() throws InterruptedException {
        if (num < 1) {
            this.wait();
        }
        num--;
        System.out.println(Thread.currentThread().getName() + "__" + num);
        this.notify();
    }
}
```

线程要做的就是加10次减10次，交替执行

```java
 public static void main(String[] args) {
        MutilPCResource resource = new MutilPCResource();
        new Thread(() -> {
            try {
                for (int i = 0; i < 10; i++) {
                    resource.add();
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"A").start();

        new Thread(() -> {
            try {
                for (int i = 0; i < 10; i++) {
                    resource.minus();
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        },"B").start();
    }
```



## 虚假唤醒



当有多个生产者和消费者时，就会出现01之外的数字

**多线程交互时(wait/notify)防止多线程的虚假唤醒**

存在于多生产者多消费者之间通过wait-notify进行交互之间，买票不存在虚假唤醒

解决方法：不适用if判断而是使用while

即if判断条件中不能使用wait，会存在虚假唤醒的问题，要使用while替代if

```java
if(){

wait();

}

while(){

wait();

}
```

**唤醒后要再进行一次判断**



为什么单个消费者和生产者不存在虚假唤醒的问题

因为生产者唤醒的不一定是消费者

阻塞队列中可能已经先后阻塞了两个生产者了，如果他俩先后唤醒，就会造成连续生产了两次，所以有多个生产者和消费者时，当唤醒时要再一次判断。





wait noyify



高内聚：将线程的资源和对资源的操作聚合到一起

低耦合：线程调用操作资源的方法，线程与资源的关系低耦合

两种操作：加和减，当小于1时加，大于0时减



# Condition

**Condition实现生产者消费者**

专注于Object monitor(wait,nitofy,notifyAll)

JUC用lock替换synchronized，condition替换Objec monitor method

Conditon可以看成是lock的钥匙

**使用Lock+Condition替换synchronized+monitor来实现生产者消费者**

(waite+notify)->(await+signal)



**为什么使用Condition？**



**新技术解决了什么问题？**

下订单->减库存->支付，三个线程之间需要按顺序执行

精准的唤醒一个线程

wait时添加一个条件(Contidion)，这样就能唤醒某个条件的线程

contition的await可以调用多次(object的wait不行)



获得Condition对象



Condition常用方法



怎么用？























# 线程池



通过三种方式来创建线程，启动线程，当任务执行的时间并不长，但是创建线程的消耗是巨大的。

所以我们可以使用线程池，提前创建好线程，自己不用再创建线程，而只需要提交任务。

创建线程池后，并不会创建线程，当任务到来时就会创建一个线程，当达到core时会存储到缓冲队列中，当缓冲队列满时，会创建新线程，达到maximumPoolSize时，会考虑放弃某些任务。







































## Executor

顶层接口，执行任务

函数式借口，用来执行Runnable

```java
void execute(Runnable command);
```



## ExecutorService

该接口继承了Executor

| 方法             | 描述                                                         |
| ---------------- | ------------------------------------------------------------ |
| shutdown         | 关闭之前提交的任务(不会等它执行完)，不再接收新任务，已经关闭了再次调用不受影响 |
| shutdownNow      | 会将等待执行的任务返回，                                     |
| isShutdown       |                                                              |
| isTerminated     |                                                              |
| awaitTermination | 阻塞到关闭请求执行完或者超时                                 |
| submit           | 可以提交Callable/Runnable，返回Future                        |
| invokeAll        | 批量提交，返回Future集合                                     |
| invokeAny        | 批量提交，返回任意一个Future                                 |



**submit**

## juc.ThreadPoolExecutor

ExecutorService的实现类

线程池的资源是很宝贵的，不推荐使用默认的参数，而要自己根据使用场景来定义具体的参数。



构造方法：

```java
public ThreadPoolExecutor(int corePoolSize,
                          int maximumPoolSize,
                          long keepAliveTime,
                          TimeUnit unit,
                          BlockingQueue<Runnable> workQueue,
                          ThreadFactory threadFactory,
                          RejectedExecutionHandler handler)
```

| 参数            | 描述                                                         |
| --------------- | ------------------------------------------------------------ |
| corePoolSize    | 线程池中保存的线程个数                                       |
| maximumPoolSize | 线程池中最多的线程个数                                       |
| keepAliveTime   | 线程数大于corePoolSize时存活的时间                           |
| unit            | keepAliveTime的单位                                          |
| workQueue       | 任务执行前保存在改队列中，该队列只支持通过execute提交的Runnable任务 |
| threadFactory   | executor创建线程时使用的工厂                                 |
| handler         | 达到线程边界和队列容量时的拒绝策略                           |



**execute与submit**

都是用来提交任务的，最大的不同在于execute没有返回结果。



**线程池的状态**

```java
private static final int RUNNING    = -1 << COUNT_BITS;
private static final int SHUTDOWN   =  0 << COUNT_BITS;
private static final int STOP       =  1 << COUNT_BITS;
private static final int TIDYING    =  2 << COUNT_BITS;
private static final int TERMINATED =  3 << COUNT_BITS;
```





**并不提倡直接使用ThreadPoolExecutor,通常使用Executors类中提供的几个静态方法来创建线程池**



## juc.Executors



### FixedThreadPool

固定大小的线程池，keepAliveTime为0，阻塞队列是一个无界队列，因此永远不可能拒绝任务，所以keepAliveTime为0无效。











### CachedThreadPool

没有线程数量限制的线程池，比较适合处理执行时间短的任务。corePoolSize为0，maximumPoolSize为无限大，意味着线程数量可以无限大；keepAliveTime为60S







### SingleThreadExecutor



### ScheduledThreadPool

定时线程

它接收SchduledFutureTask类型的任务









# 原子性操作



AtomicBoolean。。。。。。



默认初始值是false，可以通过构造函数传入



compareAndSet比较交换，如果变量的当前值与之前期望的值相同就设置值，成功返回TRUE

weakCompareAndSet，compareAndSet的替补方案，可能会失败，不保证顺序。

set，无条件的set

lazySet，不保证值能被其他线程立马看到

getAndSet，跟新值，返回旧值



AtomicInteger

getAndIncrement

getAndDecrement

incrementAndGet

decrementAndGet

getAndAdd

addAndGet



AtomicIntegerArray

数组元素可以原子性更新





































生产消费问题



并发操作问题

多个线程同时操作一定数量的资源

对资源进行操作时，先判断资源的数量



















































