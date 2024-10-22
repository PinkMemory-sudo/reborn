# **服务治理(注册中心)**



[四个注册中心的比较](https://www.jianshu.com/p/9b8a746e0d90)

传统的rpc远程调用框架中，管理每个服务与每个服务之间的依赖关系，可以实现服务的注册与发现、服务调用、负载均衡、服务容错。

 springcloud支持的注册中心有Eureka、Zookeeper、Consul、Nacos



## CAP原则

* 一致性（Consistency）
* 可用性（Availability）
* 分区容错性（Partition tolerance）

在一个分布式系统中CPA只能满足两个[阮一峰博客](https://links.jianshu.com/go?to=http%3A%2F%2Fwww.ruanyifeng.com%2Fblog%2F2018%2F07%2Fcap.html)







## 四种注册中心的比较



**eureka**

目前已经进入停更状态

遵循AP原则

Eureka Server之间是对等关系，不同于Zookeeper的leader选举，节点之间相互注册，Eureka Server 宕机，Eureka Client 的请求会自动切换到新的 Eureka Server 节点上。

Eureka的集群中，只要有一台Eureka还在，就能保证注册服务可用（保证可用性



**Zookeeper**

Apache Zookeeper遵循CP原则，保证请求的一致性和容错性，但是 Zookeeper 不能保证每次服务请求都是可达的。

Zookeeper 获取服务列表时，如果此时的 Zookeeper 集群中的 Leader 宕机了，该集群就要进行 Leader 的选举，又或者 Zookeeper 集群中半数以上服务器节点不可用，那么将无法处理该请求。



**Consul**

CP原则，保证了强一致性和分区容错性

Consul 内置了服务注册与发现框架、分布一致性协议实现、健康检查、Key/Value 存储、多数据中心方案，不再需要依赖其他工具（比如 ZooKeeper 等），使用起来也较为简单。



**Nacos**

Nacos除了服务的注册发现之外，还支持动态配置服务

Nacos = 注册中心 + 配置中心





eureka就是个servlet程序，跑在servlet容器中; Consul则是go编写而成。



























