ReentrantLock





**基本操作**





 

|                                            |                                     |
| ------------------------------------------ | ----------------------------------- |
| 创建锁                                     | ,公平锁/非公平锁,无参构造是非公平锁 |
| isFair                                     |                                     |
| 获得锁                                     | lock()                              |
| 释放锁                                     | unlock()，建议在finally中调用       |
| 线程中断标志时，抛出异常。可以用来解决死锁 | lockInterruptibly()，先获得锁       |



|                             |                  |
| --------------------------- | ---------------- |
| ReentrantLock()             | 非公平锁构造方法 |
| ReentrantLock(boolean fair) | true来构建公平锁 |
| getHoldCount                |                  |
| getQueueLength              |                  |
| getWaitQueueLength          |                  |
| hasQueuedThread             |                  |
| isHeldByCurrentThread       |                  |
| isLocked                    |                  |



lock hold count



tryLock解决死锁





















