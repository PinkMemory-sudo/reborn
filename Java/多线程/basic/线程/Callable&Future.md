

# Callable

- Callable可以在任务结束的时候提供一个返回值，Runnable无法提供这个功能
- Callable的call方法分可以抛出异常，而Runnable的run方法不能抛出异常。

**使用**

1. 实现Callable<>，指定返回值类型，重写call方法
2. 使用线程池提交任务
3. 通过future获得执行结果



# Future



## FutureTask

FutureTask即实现了Runnable，也实现了Future，所以Callable对象可以通过FutureTask来运行，也可以通过FutureTask来获得线程的执行结果