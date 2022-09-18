**Excutor**

函数式接口，方法：`void execute(Runnable command);` 

执行提交的任务，将任务提交与每个任务将如何运行(立即还是异步等)的机制解耦。excute()来提交任务，任务怎么执行有实现类决定。

例如：

可以使用

```java
Executor executor = anExecutor;
executor.execute(new RunnableTask1());
executor.execute(new RunnableTask2());
```

替换

`new Thread(new(RunnableTask())).start()`



```
Executes the given command at some time in the future.  The command may execute in a new thread, in a pooled thread, or in the calling thread, at the discretion of the {@code Executor} implementation.
```



例如：

1. 使用正在执行的线程立即执行

```java
class DirectExecutor implements Executor {
    public void execute(Runnable r) {
      r.run();
    }
  }}
```

2. 创建新的进程去执行

```java
class ThreadPerTaskExecutor implements Executor {
    public void execute(Runnable r) {
      new Thread(r).start();
    }
  }}
```

3. 许多Executor实现了对任务的复杂的调度方式和时间等



Executo





