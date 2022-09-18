synchronized, wait, notify 是任何对象都具有的同步工具



**实现了Runnable**



# 构造方法

构造方法由父线程执行，run方法由子线程执行

| 方法 | 描述                           |
| ---- | ------------------------------ |
|      | 设置线程名称有助于可读性和调试 |
|      |                                |
|      |                                |



# 静态方法

| 方法 | 描述                   |
| ---- | ---------------------- |
|      | 获得当前正在执行的线程 |
|      |                        |
|      |                        |



# 实例方法

| 方法          | 描述                                                         |
| ------------- | ------------------------------------------------------------ |
|               | 获得线程名                                                   |
| isAlive       | 判断线程是否还活着(已经启动，没有终止)                       |
| gitId         | 获得线程号，线程接续后，线程号可能被其他线程重新利用         |
| yield         | 放弃当前CPU执行权                                            |
| setPriority   | 取值范围1-10其他的会抛出异常，具有继承性，使用默认的就行，从程序逻辑上下手改吧，这个可控性不强，*setPriority*不一定起作用的，在不同的操作系统不同的jvm上，效果也可能不同 |
| interrupt     | 仅仅是将子线程标记为中断，子线程没有真正中断                 |
| isInterrupted | 判断当前线程是否被标记为中断                                 |
| setDaemon     | 设置成守护线程                                               |
| getStatus     | 获得线程的状态                                               |

守护线程： 

为其他线程服务的线程，不能当都运行，当JVM中没有其他线程时，守护线程会自动销毁





run方法不能抛出异常



创建线程的？种方式

线程池

阻塞队列

线程安全

ThreadGroup

线程的几种状态

synchronized

threadStatus

新建->就绪->运行->阻塞->死亡

阻塞分为四种:

* 同步阻塞：synchronized，锁被他人占用
* 等待阻塞：通过调用线程的wait()方法，让线程等待某工作的完成
* 其他阻塞：通过调用线程的sleep()或join()或发出了I/O请求时，线程会进入到阻塞状态。当sleep()状态超时、join()等待线程终止或者超时、或者I/O处理完毕时，线程重新转入就绪状态。



多线程在于异步执行，程序用等待执行结果直接返回，操作留给另一个线程执行。



# 线程的五种状态



# Thread



**构造方法：**

`Thread(ThreadGroup group, Runnable target, String name,long stackSize)`

group,name,stackSize都可以省略

stackSize为0表示该参数被忽略而不是值为0



**静态方法：**

`public static native void yield();`

yield 将当前正在运行的线程由运行态转为就绪态，和同一优先级的线程进行竞争。



休眠：

`public static void sleep(long millis)`

可以精确到ns

`public static void sleep(long millis, int nanos)`

休眠单位为s

`TimeUnit.SECONDS.sleep(10);`



`public static int activeCount()`当前线程的线程组中的数量



**其他方法**

`public void interrupt()`

**改变中断状态而已，它不会中断一个正在运行的线程**。一定是调用被阻塞线程的interrupt方法

如果线程被wait/join/sleep阻塞时，调用该线程的interrupt()方法，那么该线程将抛出一个 InterruptedException中断异常。

sleep

在正在执行的线程中调用其他线程的interrupt方法，使他们放弃休眠，因为sleep不释放锁，所以会直接抛异常。

wait

在正在执行的线程中调用其他线程的interrupt方法，使他们放弃等待，但前提是他们要先获得锁

join与sleep一样，线程会立即进入catch代码块



`public boolean isInterrupted()`,thread对象是否处于中断状态

`public static boolean interrupted()`  静态方法，用来获得当前京城的中断状态。

`public final native boolean isAlive()` 是否是已开启并未死亡

`public final void setPriority(int newPriority)` 设置线程优先级，线程优先级有10个等级，分别用整数1-10表示。其中1位最低优先级，10为最高优先级，5为默认值。理论上优先级越高的线程获取CPU时间片的次数越多，但并不意味着优先级高的一定能抢到。

`public final synchronized void setName`



**join**

将一个线程添加到当前正在执行的线程中，用于将两个线程合并成顺序执行的线程,可以设置等待时间long millis, int nanos。



synchronized

同步锁

synchronized关键字修饰的内容：

* {}代码块
* 修饰方法
* 修饰静态方法
* 修饰类





同步方法与同步代码块

锁对象

线程同步与线程安全

静态同步与非晶态同步

线程与锁

一个锁调用另一个锁

volatile

- Java中每个对象都有一个内置锁
- 当程序运行到非静态的synchronized同步方法上时，自动获得当前对象的锁
- 使用同步方法或者同步代码块时，要指定使用的锁对象



同步方法与非同步方法

同步方法就是当多个线程调用同一个实例中的同步方法时，只会被一个线程使用。



synchronized与volatile

JVM内存模型



**线程的六种状态**

[NEW, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING, TERMINATED]



**用户线程和守护线程**

守护线程是一种特殊线程

















