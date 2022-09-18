相比synchronazed，Lock作用范围更加灵活。



**synchronazed，Lock的区别**

* synchronazed是关键字，Lock是类
* Lock作用范围更加灵活，所以需要手工释放锁，并且在发生异常时也不会释放锁，所以加锁部分的代码要用try-catch，释放锁放在finally中





注意循环或者判断条件中的Lock



**Lock接口**

```
# 用来获取锁。如果锁已被其他 线程获取，则进行等待
void lock();
void lockInterruptibly() throws InterruptedException;
# 尝试获取锁：成功获取则返回true；获取失败则返回false
boolean tryLock();
boolean tryLock(long time, TimeUnit unit) throws InterruptedException; void unlock();
Condition newCondition();
```



## Condition

wait()/notify()这两个方法一起使用可以实现等待/通 知模式，使用 Condition 类可以 进行选择性通知。

Condition,加强版的wait/nodity，只唤醒相同Condition对象。

- await()会使当前线程等待,同时会释放锁,当其他线程调用 signal()时,线程会重 新获得锁并继续执行。
- signal()用于唤醒一个等待的线程。



# 使用



**格式**

```
Lock lock = ...; 
lock.lock(); 
try{
//处理任务
}catch(Exception ex){ 
}finally{
lock.unlock();
}
```























加锁相当于加了个执行条件，不仅要获得CPU执行权，还要有锁才能执行，这样保证了当有锁的线程执行完别的线程才能执行。



## **ReentrantLock**



什么是可重入锁？synchronized可重入吗

一个线程在获得锁对象后，再次申请锁对象能否获得锁对象。不可重入时，就会产生死锁





## **ReadWriteLock**



接口

```
Lock readLock();
Lock writeLock();
```

好处：

* 如果当前写锁没有被占用，则多线程去读时相当于不加锁，如果写锁会占用，会阻塞
* 如果当前读锁被占用，申请写锁时会阻塞



## **ReentrantReadWriteLock**



# 线程通信

两种方式：

* 共享内存
* 消息传递



wait/notify总结：

* 在执行方法前，先判断条件是否满足，不满足需要wait，满足再想下执行。
* 执行完之后需要notify，通知其他线程

























# 容器





## CopyOnWriteArrayList



它相当于线程安全的 ArrayList，其中所有可变操作（add,set等）都是通过底层数组的进行一次新的复制产生的。

适合经常查询不经常修改的场景





# CountDownLatch



适用场景：n个任务完成后，才进行下一步操作



创建CountDownLatch，构造方法指定n

完成一个任务时，执行countDownLatch.countDown();

主线程countDownLatch.await();

计数器为0时，会唤醒父线程































# CyclicBarrier

循环阻塞，屏蔽够n个线程后才继续进行



等到所有线程都到达一个屏障点才退出await方法

































# Semaphore



2个停车位，3两汽车。

类似与Lock，不过它有多把锁。

构造方法指定最大信号量：Semaphore semaphore = new Semaphore(1);

线程需要操作资源时，必须现货的整数semaphore.acquire();

处理完后通过semaphore.release();释放。

































# BlockingQueue

ArrayBlockingQueue定长的阻塞队列

ArrayBlockingQueue 在生产者放入数据和消费者获取数据，都是共用同一个 锁对象，由此也意味着两者无法真正并行运行



**DelayQueue**到时间后才能被获取



























# CompletableFuture