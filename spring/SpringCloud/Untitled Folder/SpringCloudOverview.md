# SpringCloud



SpringCloud+SpringCloud alibaba

* 为什么要使用分布式
* 什么是微服务



**什么是微服务，有什么好处**

* 将一个应用拆分成多个小应用，彼此之间相互配合
* 根据需求，部署不同数量的服务





**什么是SpringCloud**



* 服务注册与发现
* 服务注册
* 服务熔断
* 负载均衡
* 服务降级
* 服务的消息队列
* 配置中心
* 服务网关 
* 服务监控
* 链路追踪
* 自动化构建部署
* 服务定时任务调度



![1636637828600](.\imgs\1636637828600.png)



**升级：**

![1636637736845](.\imgs\1636637736845.png)



# 服务注册中心

 

* Eureka
* Zookeeper
* Consul
* Nacos

分成了多个微服务，需要一个统一管理的中心。



为什么需要注册中心

微服务都需要将自己注册到服务中心来提供或调用服务，就像菜市场，买菜的和卖菜的都很多，为了汇总资源和统一管理，都来菜市场交易。



**服务治理**

多个服务提供者和调用者



# 服务调用



* Feign
* OpenFeign



* Ribbon
* LoadBalancer



# 服务降级

熔断，限流

* Hystrix
* resilience4j
* sentinel



# 网关

* zuul
* gateway



# 服务配置

* Config
* Nacos



# 服务总线

* Bus
* Nacos