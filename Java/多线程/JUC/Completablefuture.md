可以用来做什么





# **CompletionStage**

代表异步计算中的一个阶段或步骤，一个stage可以用来结束上一个计算，也可以用来触发下一个stage。一个stage执行的计算可以是一个Function，Consumer或者是一个Runnable(取决于是否有参数和返回值)。

CompletionStage 的接口一般都返回新的CompletionStage，表示执行完一些逻辑后，生成新的CompletionStage，构成链式的阶段型的操作。



**apply**

apply接受一个函-数**

accept接受一个消费者



**accept**



**run**



**async**

每个方法都有一个代async后缀的方法，表示异步执行，可以使用自定义的Executor



 **BiConsumer** 



# CompletableFuture



**创建CompletableFuture**





## 串行执行



## **并行执行**