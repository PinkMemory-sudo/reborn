**与Spring的Schedule相比**

Quartz支持持久化



## 四个核心概念

* Job：函数是接口，实现要定时执行的任务
* JobDetail：封装Job的类信息，属性和值的信息等
* Trigger：描述触发Job的规则。主要有 SimpleTrigger 和 CronTrigger 这两个子类。当且仅当需调度一次或者以固定时间间隔周期执行调度，SimpleTrigger 是最适合的选择；而 CronTrigger 则可以通过 Cron 表达式定义出各种复杂时间规则的调度方案：如工作日周一到周五的 15：00 ~ 16：00 执行调度等。

- Scheduler：调度器。所有的调度都是由它控制，装载着任务和触发器。



# 使用

1. 添加依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-quartz</artifactId>
</dependency>
```

2. 定义一个Job

