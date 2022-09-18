关键字synchronized与wait和notify方法相结合可以实现等待/通知机制，ReentrantLock与Condition结合也可以实现等待/通知模式。

Condition可以实现**多路通知**功能，有选择性的进行通知。一个Lock对象里可以创建多个Condition(通过调用该Lock的newCondition方法)。





**使用多个Condition通知局部线程**





**生产者消费者模式实现一对一交替打印**



**生产者消费者模式实现多对多交替打印**