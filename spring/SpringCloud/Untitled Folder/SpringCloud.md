配置管理

服务发现

熔断

负载均衡

控制总线

一次性令牌

全局锁

leadership election

distributed sessions

cluster state



## Features

Distributed/versioned configuration

Service registration and discovery

Routing

Service-to-service calls

Load balancing

Circuit Breakers

Global locks

Leadership election and cluster state

Distributed messaging





### Adding Spring Cloud To An Existing Spring Boot Application

1. 确定应该使用的SpringCloud版本，取决于当前使用的SpringBoot版本

| Release Train                                                | Boot Version                     |
| :----------------------------------------------------------- | :------------------------------- |
| [Hoxton](https://github.com/spring-projects/spring-cloud/wiki/Spring-Cloud-Hoxton-Release-Notes) | 2.2.x, 2.3.x (Starting with SR5) |
| [Greenwich](https://github.com/spring-projects/spring-cloud/wiki/Spring-Cloud-Greenwich-Release-Notes) | 2.1.x                            |
| [Finchley](https://github.com/spring-projects/spring-cloud/wiki/Spring-Cloud-Finchley-Release-Notes) | 2.0.x                            |
| [Edgware](https://github.com/spring-projects/spring-cloud/wiki/Spring-Cloud-Edgware-Release-Notes) | 1.5.x                            |
| [Dalston](https://github.com/spring-projects/spring-cloud/wiki/Spring-Cloud-Dalston-Release-Notes) | 1.5.x                            |

 Dalston, Edgware, and Finchley 停止维护了

使用对应SpringCloud的最新SR(服务版本)



SpringCloudConfig

通过git repository，进行集中式外部配置管理















Spring Cloud Bus

事件总线，用于将服务和服务实例与分布式消息传递链接在一起，用于在集群中传播状态更改，例如配置改变事件



Spring Cloud Cloudfoundry

提供服务发现实现，并使实现SSO和OAuth2受保护的资源变得容易。



Spring Cloud Open Service Broker

？



Spring Cloud Cluster

抽象和实现for Zookeeper, Redis, Hazelcast, Consul.



Spring Cloud Consul

使用Hashicorp Consul进行服务发现和配置管理



Spring Cloud Security

提供对负载平衡的OAuth2 rest客户机和身份验证头中继的支持Zuul proxy



Spring Cloud Sleuth

链路追踪



Spring Cloud Data Flow

A cloud-native orchestration service for composable microservice applications on modern runtimes. Easy-to-use DSL, drag-and-drop GUI, and REST-APIs together simplifies the overall orchestration of microservice based data pipelines.



Spring Cloud Stream

轻量级事件驱动的微服务框架，用于快速构建可以连接到外部系统的应用程序，简单的声明式模型，使用Apache Kafka或RabbitMQ在Spring Boot应用程序之间发送和接收消息。



Spring Cloud Stream App Starters

Spring Cloud Stream App Starters are Spring Boot based Spring Integration applications that provide integration with external systems



Spring Cloud Task

A short-lived microservices framework，用于快速构建执行有限数量数据处理的应用程序。Simple declarative for adding both functional and non-functional features to Spring Boot apps.



Spring Cloud Task App Starters

Spring Cloud Task App Starters are Spring Boot applications that may be any process including Spring Batch jobs that do not run forever, and they end/stop after a finite period of data processing.



Spring Cloud Zookeeper

使用Apache Zookeeper进行服务发现和配置管理。



Spring Cloud Connectors

Makes it easy for PaaS applications in a variety of platforms to connect to backend services like databases and message brokers (the project formerly known as "Spring Cloud")



Spring Cloud Starters
Spring Boot-style starter projects to ease dependency management for consumers of Spring Cloud. (Discontinued as a project and merged with the other projects after Angel.SR2.)



Spring Cloud CLI

Spring Boot CLI plugin for creating Spring Cloud component applications quickly in Groovy





Spring Cloud Gateway





# SpringCloud Alibaba





Nacos Config, Nacos Discovery、 Sentinel， OSS ， Spring Cloud Stream RocketMQ Binder，Spring Cloud Bus RocketMQ，Dubbo Spring Cloud、Seata 组件。颠覆性地使用 Spring Cloud 客户端调用 Dubbo 服务，spring-cloud-alibaba-sentinel-gateway 模块以完善 Sentinel 对 Spring Cloud 体系 Gateway 的支持。

## Features

[Alibaba Sentinel](https://github.com/alibaba/Sentinel/)：流控制和服务降级，circuit breaking

[Alibaba Nacos](https://github.com/alibaba/nacos/)：

* 服务注册和发现，支持Ribbon负载均衡
* 分布式配置，using [Alibaba Nacos](https://github.com/alibaba/nacos/) as a data store



 [RocketMQ](https://rocketmq.apache.org/)：**Event-driven**，构建高度可伸缩的事件驱动微服务





Feign可以与Eureka和Ribbon组合使用以支持负载均衡。



## nacos

集注册中心与配置中心于一身



***CircuitBreaker,熔断器***

作用于服务调用方

实现快速错误，如果它在一段时间内侦测到许多类似的错误，会强迫其以后的多个调用快速失败，不再访问远程服务器。

断路器很好理解, 当Hystrix Command请求后端服务失败数量超过一定比例(默认50%), 断路器会切换到开路状态(Open). 这时所有请求会直接失败而不会发送到后端服务. 断路器保持在开路状态一段时间后(默认5秒), 自动切换到半开路状态(HALF-OPEN). 这时会判断下一次请求的返回情况, 如果请求成功, 断路器切回闭路状态(CLOSED), 否则重新切换到开路状态(OPEN).



***Fallback***

降级。 对于查询操作, 我们可以实现一个fallback方法, 当请求后端服务出现异常的时候, 可以使用fallback方法返回的值. fallback方法的返回值一般是设置的默认值或者来自缓存.



**资源隔离**

在Hystrix中, 主要通过线程池来实现资源隔离. 通常在使用的时候我们会根据调用的远程服务划分出多个线程池. 例如调用产品服务的Command放入A线程池, 调用账户服务的Command放入B线程池. 这样做的主要优点是运行环境被隔离开了. 这样就算调用服务的代码存在bug或者由于其他原因导致自己所在线程池被耗尽时, 不会对系统的其他服务造成影响. 但是带来的代价就是维护多个线程池会对系统带来额外的性能开销. 如果是对性能有严格要求而且确信自己调用服务的客户端代码不会出问题的话, 可以使用Hystrix的信号模式(Semaphores)来隔离资源.



因为，Feign中已经依赖了Hystrix所以在maven配置上不用做任何改动。



***Hystrix-dashboard***

一款针对Hystrix进行实时监控的工具，通过Hystrix Dashboard我们可以在直观地看到各Hystrix Command的请求响应时间, 请求成功率等数据。但是只使用Hystrix Dashboard的话, 你只能看到单个应用内的服务信息, 这明显不够. 我们需要一个工具能让我们汇总系统内多个服务的数据并显示到Hystrix Dashboard上, 这个工具就是Turbine.



hystrix-dashboard帮我们封装好了hystrix的监控面板。



***



## Spring Cloud Config

它包含了Client和Server两个部分，server提供配置文件的存储、以接口的形式将配置文件的内容提供出去，client通过接口获取数据、并依据此数据初始化自己的应用。Spring cloud使用git或svn存放配置文件，默认情况下使用git





























服务发现——Netflix Eureka

**Feign的一个关键机制就是使用了动态代理**

Feign的动态代理会根据你在接口上的@RequestMapping等注解，来动态构造出你要请求的服务的地址

客服端负载均衡——Netflix Ribbon

帮你在每次请求时选择一台机器，均匀的把请求分发到各个机器上 



首先Ribbon会从 Eureka Client里获取到对应的服务注册表，也就知道了所有的服务都部署在了哪些机器上，在监听哪些端口号。

然后Ribbon就可以使用默认的Round Robin算法，从中选择一台机器

Feign就会针对这台机器，构造并发起请求





断路器——Netflix Hystrix

某一个服务挂了，就会引起连锁反应，导致别的服务也挂。

Hystrix是隔离、熔断以及降级的一个框架。

隔离：Hystrix会搞很多个小小的线程池，比如订单服务请求库存服务是一个线程池，请求仓储服务是一个线程池，请求积分服务是一个线程池。每个线程池里的线程就仅仅用于请求那个服务。

熔断：直接报错

降级：报错了自己做个简单的实现



服务网关——Netflix Zuul

负责网络路由

有一个网关之后，还有很多好处，比如可以做统一的降级、限流、认证授权、安全。

提供一个对外接口



# Spring Cloud Bus



一个传输工具

Spring Cloud Bus用轻量级消息代理连接分布式系统的节点，可以用来广播状态的更改和其他管理指令，如在配置发生更改的时候。AMQP和Kafka代理实现包含在这个项目中。



使用：

Spring Boot application 默认尝试去连接一个RabbitMQ server(localhost:5672,the default value of spring.rabbitmq.addresses),

























# Spring Cloud CLI



Spring Boot CLI为Spring Cloud提供SpringBoot命令行特性,

可以编写Groovy脚本来运行Spring Cloud组件应用程序(例如@EnableEurekaServer)。

还可以轻松地执行加密和解密等操作，以支持使用秘密配置值的Spring Cloud配置客户端。

有了启动器CLI，可以从命令行一次性方便地启动Eureka、Zipkin、Config Server等服务



# Spring Cloud - Cloud Foundry Service Broker



云计算框架



# Spring Cloud Commons



Spring Cloud Commons两个库：Spring Cloud Context and Spring Cloud Commons

Spring Cloud Context为Spring Cloud应用程序的ApplicationContext(引导上下文、加密、刷新范围和环境端点)提供了实用程序和特殊服务。

Spring Cloud Commons是在不同的Spring Cloud实现中使用的一组抽象和公共类(例如 Spring Cloud Netflix vs. Spring Cloud Consul)。



## Features



### Spring Cloud Context features:

- Bootstrap Context
- `TextEncryptor` beans
- Refresh Scope
- Spring Boot Actuator endpoints for manipulating the `Environment`

### Spring Cloud Commons features:

- `DiscoveryClient` interface
- `ServiceRegistry` interface
- Instrumentation for `RestTemplate` to resolve hostnames using `DiscoveryClient`





# Spring Cloud Config

分布式配置，我们放在配置文件中，当修改了配置时，必须重新启动才能生效，有很多强大的配置中心，

外部化配置提供server and client，有了配置服务器，可以***集中的管理所有环境配置***，服务器存储后端的默认实现使用git，因此它很容易支持带标签的配置环境版本，并且可以访问用于管理内容的各种工具，但是添加替代实现并将其插入到Spring配置中是很容易的。

也可以从本地中存储spring.profiles.active=native，它会从项目的 **resources**路径下读取配置文件，也可以指定spring.cloud.config.server.native.searchLocations = file:D:/properties/来读取。



## Features

* HTTP, resource-based API for external configuration (name-value pairs, or equivalent YAML content)

* 加密解密属性值

* 可以使用@EnableConfigServer轻松嵌入到Spring引导应用程序中

Config Client features：

* 绑定到配置服务器，用远程的属性来初始化spring环境
* Encrypt and decrypt property values 



## 使用

Spring Boot application将尝试与spring.cloud.config.uri的默认值http://localhost:8888配置服务器进行连接，可以在bootstrap.[yml | properties]中修改

​	比如@Value("${config.name}")可以从本地的配置文件或者远程配置服务器中获取。默认情况下，配置服务器中的属性值优先于本地的属性值。

1. 添加spring-cloud-config-server依赖
2. 添加@EnableConfigServer
3. 需要一个spring.cloud.config.server.git.uri来定位需要的配置数据(默认情况下，它是一个git存储库的位置，可以是一个本地`file:..`URL)



### 服务端

通过指定的配置中心来管理应用资源，server提供配置文件的存储、以接口的形式将配置文件的内容提供出去。

在启动的时候从配置中心获取和加载配置信息配置，服务器默认采用git来存储配置信息，这样就有助于对环境配置进行版本管理，并且通过git客户端工具来方便的管理和访问配置内容。

定位属性资源的默认策略时clone一个git仓库(spring.cloud.config.server.git.uri)，然后初始化一个mini SpringApplication，enumerate property sources and publish them at a JSON endpoint.。

除了 git 外，还可以用数据库、svn、本地文件等作为存储。



***使用***

1. 添加依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
 </dependency>
```

2. 启动类上加注解@EnableConfigServer

3. 配置文件：

```yml
spring:
  cloud:
    config:
      server:
        git:
          ### git 地址
          uri: https://gitee.com/naimaohome/****.git
          ## 配置文件的目录
          search-paths:
            - config
          ### 账户和密码，公有库可以省略  
          username: *****
          password: *****
      ##读取分支名
      label: main
```

4. 可以通过host:port/分支名/配置文件名来访问属性

***配置属性的读取规则***

1. {label}/{application}-{profiles}.yml

如：`http://172.18.17.34:3301/main/consumer-dev.yml`

2. {application}-{profiles}.yml， 默认从配置的分支读取

如：`http://172.18.17.34:3301/consumer-dev.yml`

3. /{application}/{profiles}/{label}

如：`http://172.18.17.34:3301/consumer-dev/main`

除了配置文件的内容外，还显示了git的相关信息



***bootstrap与application***

1. 加载顺序



bootstrap.yml与application.yml都应该写什么

* bootstrap中写固定不变的，application中写可以替换的
* 





### 客户端

applicaiton.ym1是用户级的资源配置项，bootstrap.yml是系统级的，优先级更加高，内容不会被application覆盖。

Spring Cloud会创建一个“Bootstrap Context”，作为Spring应用的Application Context的父上下文。初始化的时候，Bootstrap Context负责从外部源加载配置属性并解析配置。这两个上下文共享一个从外部获取的'Environment'。

bootstrap.yml（bootstrap.properties）用来在程序引导时执行，应用于更加早期配置信息读取，如可以使用来配置application.yml中使用到参数等

application.yml（application.properties) 应用程序特有配置信息，可以用来配置后续各个模块中需使用的公共参数等。

bootstrap.yml 先于 application.yml 加载

bootstrap.yml来修改启动行为

***要将Client模块下的application.yml文件改为bootstrap.yml,这是很关键的***



***使用***

1. 添加依赖

```xml
	<dependency>
		<groupId>org.springframework.cloud</groupId>
		<artifactId>spring-cloud-starter-config</artifactId>
	</dependency>
```

2. 启动类上加
3. bootstrap.yml

```

```







## 配置服务器

git url 指定配置文件所在的项目,它会再拼接上search-paths+applacation.name



## 配置客户端





# Spring Cloud Connectors



简化了连接过程，该项目进入了维护模式



# Spring Cloud Consul



作用：

* 服务发现与注册
* 分布式配置：使用Consul的键值对存储
* 控制总线：通过Consul Events实现分布式事件控制
* 客户负载均衡Ribbin或Spring Cloud LoadBalancer
* 支持动态路由过滤器Zuul
* 

Consul，领事，执行官

Spring Cloud Consul 是把 [Consul](http://www.oschina.net/p/consul) 的所有特性都集成到 Spring Cloud 系统中。

通过几个简单的注释，就可以快速启用和配置应用程序中的公共模式，提供的模式包括服务发现、分布式配置和控制总线。



Spring Cloud Consul features:

- Service Discovery: instances can be registered with the Consul agent and clients can discover the instances using Spring-managed beans
- Supports Ribbon, the client side load-balancer via Spring Cloud Netflix
- Supports Spring Cloud LoadBalancer - a client side load-balancer provided by the Spring Cloud project
- Supports Zuul, a dynamic router and filter via Spring Cloud Netflix
- Distributed Configuration: using the Consul Key/Value store
- Control Bus: Distributed control events using Consul Events



使用：

@EnableDiscoveryClient，会去连接Consul agent，localhost:8500(the default values of spring.cloud.consul.host and spring.cloud.consul.port)

1. 安装consul

# Spring Cloud Gateway



特性：

- Built on Spring Framework 5, Project Reactor and Spring Boot 2.0
- Able to match routes on any request attribute.
- Predicates and filters are specific to routes.
- 集成了Hystrix Circuit Breaker.
- 集成了Spring Cloud DiscoveryClient 
- Easy to write Predicates and Filters
- 请求速率限制
- Path Rewriting 路径校对



使用：

添加spring-cloud-starter-gateway依赖:

org.springframework.cloud

spring-cloud-starter-gateway

关闭网关：spring.cloud.gateway.enabled=false



***注意：***

Spring Cloud Gateway构建在Spring Boot 2上，Spring WebFlux和Project Reactor。作为一个后果，在使用Spring Cloud Gateway时，您所熟悉的许多同步库(例如Spring Data和Spring Security)和模式可能并不适用。

Spring Cloud Gateway需要Spring Boot和Spring Webflux提供的Netty运行时。它不能在传统Servlet容器中工作，也不能在构建为WAR时工作



三个概念：

**Route**:由id，uri，断言，过滤器定义。

**Predicate**: 断言通过时才匹配路由，可以匹配来自HTTP请求的任何内容，例如头或参数。

**Filter**:可以在路由前后对请求和响应做出改变，发出代理请求后，将运行post过滤器逻辑

`路由中没有指定端口时，http默认80，https默认443`



Spring Cloud Gateway将路由匹配为Spring WebFlux HandlerMapping基础设施的一部分

可以使用and语句组合多个路由谓词工厂



## Predicate Factory



***After***:

​	接收一个datetime的参数(java 8的ZonedDateTime，带时区的日期时间)，指定时间之后的请求可以通过

```yml
spring:
  cloud:
    gateway:
      routes:
      - id: after_route
        uri: https://example.org
        predicates:
        - After=2017-01-20T17:42:47.789-07:00[America/Denver]
```

predicates接收一个list。

***Before，Betweed*** 同理

```yml
spring:
  cloud:
    gateway:
      routes:
      - id: between_route
        uri: https://example.org
        predicates:
        - Between=2017-01-20T17:42:47.789-07:00[America/Denver], 2017-01-21T17:42:47.789-07:00[America/Denver]
```



***Cookie***

cookie断言需要两个参数：name，regexp。给定名称的值与regexp匹配时通过

```yml
spring:
  cloud:
    gateway:
      routes:
      - id: cookie_route
        uri: https://example.org
        predicates:
        - Cookie=chocolate, ch.p
```

cookie中名字是chocolate的缓存，值匹配ch.p时通过



***Header***

需要两个参数，与cookie同理



***Host***

需要一个参数，一个host模式list,支持ant

```yml
spring:
  cloud:
    gateway:
      routes:
      - id: host_route
        uri: https://example.org
        predicates:
        - Host=**.somehost.org,**.anotherhost.org
```



***Method***

需要一个或多个参数

```yml
spring:
  cloud:
    gateway:
      routes:
      - id: method_route
        uri: https://example.org
        predicates:
        - Method=GET,POST
```



***Path***

two parameters,

a list of Spring PathMatcher patterns 

an optional flag called `matchOptionalTrailingSeparator`

```yml
spring:
  cloud:
    gateway:
      routes:
      - id: path_route
        uri: https://example.org
        predicates:
        - Path=/red/{segment},/blue/{segment}
```

/red/hello可以通过，/red不会，/red/hello/user也不会。可以使用ant

/red/hello会转发到`https://example.org/red/hello`

即path的值会追加到uri后面

predicate会提取出URI template中的变量(比如hello)，作为names，values映射放到ServerWebExchange.getAttributes()中定义的ServerWebExchangeUtils.URI_TEMPLATE_VARIABLES_ATTRIBUTE中，最后会被GatewayFilter factories使用

，get可以获取到

```java
Map<String, String> uriVariables = ServerWebExchangeUtils.getPathPredicateVariables(exchange);

String segment = uriVariables.get("segment");
```



***Query***

根据请求参数进行匹配

two parameters：请求参数名和可选的regexp

```yml
spring:
  cloud:
    gateway:
      routes:
      - id: query_route
        uri: https://example.org
        predicates:
        - Query=red, gree.
```

请求参数有red，值与gree匹配时通过



***RemoteAddr***

```yml
spring:
  cloud:
    gateway:
      routes:
      - id: remoteaddr_route
        uri: https://example.org
        predicates:
        - RemoteAddr=192.168.1.1/24
```



***Weight***

```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: weight_high
        uri: https://weighthigh.org
        predicates:
        - Weight=group1, 8
      - id: weight_low
        uri: https://weightlow.org
        predicates:
        - Weight=group1, 2
```

主要用来配置权重，80%会转发到`https://weighthigh.org`



使用服务名而不是uri进行转发

```java
spring:
  application:
    name: spring-cloud-gateway
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true
      routes:
        - id: multi_route
          # uri的值编程lb://服务名
          uri: lb://CONSUMER0
          # 一组断言: 请求方法为get，路径为/hello/**,
          predicates:
            - Method=GET
            - Path=/user/**
            - After=2017-01-20T17:42:47.789-07:00[America/Denver]  
```



# Spring Cloud LoadBalancer



# Spring Cloud OpenFeign

声明式的web service client，只需要写接口再加上注解

Spring Cloud集成了Ribbon和Eureka，以及Spring Cloud load均衡器，在使用Feign时提供负载平衡的http客户端，包含hystrix

Feign已经默认使用了Ribbon

Spring Cloud Netflix Ribbon现在处于维护模式，所以我们建议使用Spring Cloud load均衡器。



**使用**

***1. 引入OpenFeign***

org.springframework.cloud

spring-cloud-starter-openfeign

***2. 启动类上加***@EnableFeignClients

***3. 写接口***

```java
@FeignClient("服务名")
public interface StoreClient {
    @RequestMapping(method = RequestMethod.GET, value = "/stores")
    List<Store> getStores();

    @RequestMapping(method = RequestMethod.GET, value = "/stores")
    Page<Store> getStores(Pageable pageable);

    @RequestMapping(method = RequestMethod.POST, value = "/stores/{storeId}", consumes = "application/json")
    Store update(@PathVariable("storeId") Long storeId, Store store);
}
```

@FeignClient同时还创建了一个负载均衡器( Ribbon或者Spring Cloud LoadBalancer)

除了指定服务名外，还可以是一个ur了，以前，使用url属性不需要name属性。现在需要使用名称

应用程序上下文中bean的名称是接口的完全限定名。



可以声明额外的配置

```java
// The serviceId attribute is now deprecated in favor of the name attribute.
@FeignClient(name = "stores", configuration = FooConfiguration.class)
public interface StoreClient {
    //..
}
```

configuration指定一个配置类，将使用默认的FeignClientsConfiguration和自定义的FooConfiguration一起创建客户端，后者的东西会覆盖前者。

支持占位符来获得配置文件中的属性

```java
@FeignClient(name = "${feign.name}", url = "${feign.url}")
public interface StoreClient {
    //..
}
```



自定义的FooConfiguration不能加@Configuration注解，加@Configuration不能包含在@ComponentScan下，否则它会成为feign.Decoder`, `feign.Encoder`, `feign.Contract的默认配置来源。



可以使用配置文件自定义一个

```yml
feign:
  client:
    config:
      # default:自定义所有,feign的Name:定义一个
      feign的Name:
        connectTimeout: 5000
        readTimeout: 5000
        loggerLevel: full
        errorDecoder: com.example.SimpleErrorDecoder
        retryer: com.example.SimpleRetryer
        requestInterceptors:
          - com.example.FooRequestInterceptor
          - com.example.BarRequestInterceptor
        decode404: false
        encoder: com.example.SimpleEncoder
        decoder: com.example.SimpleDecoder
        contract: com.example.SimpleContract
```

Yaml的优先级大于自定义的配置类大于默认

feign.client.default-to-properties=false可以改变优先级



禁止hystrix

```yml
feign:
  hystrix:
    enabled: false
```



```yml
@FeignClient(contextId = "fooClient", name = "stores", configuration = FooConfiguration.class)
public interface FooClient {
    //..
}

@FeignClient(contextId = "barClient", name = "stores", configuration = BarConfiguration.class)
public interface BarClient {
    //..
}
```

如果用相同的服务名不同的配置创建cilent，必须用contextId来区别



上述创建client的方法不适用时，可以手动创建

```java
// FeignClientsConfiguration.class是Spring Cloud OpenFeign提供的默认配置
@Import(FeignClientsConfiguration.class)
class FooController {

    private FooClient fooClient;

    private FooClient adminClient;

        @Autowired
    public FooController(Decoder decoder, Encoder encoder, Client client, Contract contract) {
        this.fooClient = Feign.builder().client(client)
                .encoder(encoder)
                .decoder(decoder)
                .contract(contract)
                .requestInterceptor(new BasicAuthRequestInterceptor("user", "user"))
          			// PROD-SVC是服务名
                .target(FooClient.class, "https://PROD-SVC");

        this.adminClient = Feign.builder().client(client)
                .encoder(encoder)
                .decoder(decoder)
                .contract(contract)
                .requestInterceptor(new BasicAuthRequestInterceptor("admin", "admin"))
                .target(FooClient.class, "https://PROD-SVC");
    }
}
```



### [Feign Hystrix Support](https://docs.spring.io/spring-cloud-openfeign/docs/2.2.5.RELEASE/reference/html/#spring-cloud-feign-hystrix)

feign.hystrix.enabled=true，



***fallback***

当熔断时执行的代码

编写实现类(熔断时调用)，然后使用@FeignClient的fallback属性指定这个类

```java
@FeignClient(name = "hello", fallback = HystrixClientFallback.class)
protected interface HystrixClient {
    @RequestMapping(method = RequestMethod.GET, value = "/hello")
    Hello iFailSometimes();
}

static class HystrixClientFallback implements HystrixClient {
    @Override
    public Hello iFailSometimes() {
        return new Hello("fallback");
    }
}

```



如果需要熔断的原因，实现FallbackFactory<HystrixClient>

```java
@FeignClient(name = "hello", fallbackFactory = HystrixClientFallbackFactory.class)
protected interface HystrixClient {
    @RequestMapping(method = RequestMethod.GET, value = "/hello")
    Hello iFailSometimes();
}

@Component
static class HystrixClientFallbackFactory implements FallbackFactory<HystrixClient> {
    @Override
    public HystrixClient create(Throwable cause) {
        return new HystrixClient() {
            @Override
            public Hello iFailSometimes() {
                return new Hello("fallback; reason was: " + cause.getMessage());
            }
        };
    }
}
```

fallbacks目前有一个***局限*** ：不支持返回com.netflix.hystrix.HystrixCommand and rx.Observable。



***@primary***

Spring Cloud OpenFeign将所有Feign实例标记为@Primary，因此Spring Framework将知道要注入哪个bean。 在某些情况下，这可能不是理想的。

```java
@FeignClient(name = "hello", primary = false)
public interface HelloClient {
    // methods here
}
```



***继承***

Feign通过单继承接口支持样板api。这允许将公共操作分组到方便的基接口中

UserService.java

```java
public interface UserService {

    @RequestMapping(method = RequestMethod.GET, value ="/users/{id}")
    User getUser(@PathVariable("id") long id);
}
```

UserResource.java

```java
@RestController
public class UserResource implements UserService {

}
```

UserClient.java

```java
package project.user;

@FeignClient("users")
public interface UserClient extends UserService {

}
```

不推荐再服务器和客户端之间共享接口，它增加的耦合性，不能使用Spring MVC(method parameter mapping is not inherited)



***压缩请求/响应***

开启属性(默认是关闭的)

```java
feign.compression.request.enabled=true
feign.compression.response.enabled=true
```



压缩请求

```yml
feign.compression.request.enabled=true
feign.compression.request.mime-types=text/xml,application/xml,application/json
feign.compression.request.min-request-size=2048
```

对于除了OkHttpClient之外的http客户端，默认的gzip解码器可以解压UTF-8编码gzip响应

```java
feign.compression.response.enabled=true
feign.compression.response.useGzipDecoder=true
```



### [Feign logging](https://docs.spring.io/spring-cloud-openfeign/docs/2.2.5.RELEASE/reference/html/#feign-logging)

每个client都创建了一个logger，logger的名字默认是创建client接口的权限定类名，只响应DEBUG级别。

负载均衡

服务器端的负载均衡

nginx 是客户端所有请求统一交给 nginx，由 nginx 进行实现负载均衡请求转发，属于服务器端负载均衡。既请求由 nginx 服务器端进行转发。



**Ribbon**

是讲服务注册信息列表缓存到本地，在本地实现负载均衡策略，是进程内的负载均衡。

***Nginx*** 适合于服务器端实现负载均衡 比如 Tomcat ，Ribbon 适合与在微服务中 RPC 远程调用实现本地服务负载均衡



Ribbon自带的7中负载均衡策略：

![image-20201027145219263](/Users/chenguanlin/Documents/note/0img/ribbon默认的7中负载均衡.png)

轮训

随机

重试：先轮训，规定时间没有响应时重试，直到获得可用服务

权重：响应速度越快，权重越大，越容易被选中

先过滤掉因多次访问故障而熔断的服务，然后选择一个并发量最小的一个服务。



Ribbon的使用：

1. 添加配置类

```java
@Configuration
public class MyRibbonRule {
    @Bean
    public IRule randomRule() {
        return new RandomRule();
    }
}
```

官方文档明确给出了**警告**:
这个自定义配置类不能放在@ComponentScan所扫描的当前包下以及子包下，
否则我们自定义的这个配置类就会被所有的Ribbon客户端所共享，达不到特殊化定制的目的了。























































* Spring config ，Nacos配置  自动更新配置，并且对版本做出相应的控制
* Spring Cloud Bus   广播状态改变消息
* **actuator**
* RocketMQ，消息管理系统



明日：

Seata：分布式事务，



注册中心：

尝试手工配置每个服务时很痛苦的



# Spring Cloud Sleuth



出现背景：

一个请求可能需要调用很多个服务，出错时难以定位，所以出现了***分布式请求链路追踪***，SpringCloud提供了自动配置	



控制台显式debug日志

```properties
logging.level.org.springframework.web.servlet.DispatcherServlet=DEBUG
```





where trace data  are reported to？how many traces to keep？ if remote fields  are sent, and which libraries are traced？

特性：

* 将trace and span ids添加到Slf4J MDC，
* 检查Spring应用程序常见的出入点(servlet filter, rest template, scheduled actions, message channels, feign client)
* 如果spring-cloud-sleuth-zipkin可用，将会通过HTTP生成报告 Zipkin compatible traces，默认port 9411
* Sleuth默认速率受限的采样器每秒抽样1000个事务



术语：

***术语***

* Span：链路追踪工作的基本单元，发送一个远程调度任务 就会产生一个Span，Span是一个64位ID唯一标识的，是trace的一部分；Span中有ID，时间戳，描述，进程ID(通常是Ip地址)，第一个Span的ID就是TraceID；
* Trace：一系列Span组成的一个树状结构。请求一个微服务系统的API接口，这个API接口，需要调用多个微服务，调用每个微服务都会产生一个新的Span，所有由这个请求产生的Span组成了这个Trace；
* Annotation：用来及时记录一个事件的，一些核心注解用来定义一个请求的开始和结束，如：
  * cs - Client Sent -客户端发送一个请求，这个注解描述了这个Span的开始
  * sr - Server Received -服务端获得请求并准备开始处理它，如果将其sr减去cs时间戳便可得到网络传输的时间。
  * ss - Server Sent （服务端发送响应）–该注解表明请求处理的完成(当请求返回客户端)，如果ss的时间戳减去sr时间戳，就可以得到服务器请求的时间
  * cr - Client Received （客户端接收响应）-此时Span的结束，如果cr的时间戳减去cs时间戳便可以得到整个请求所消耗的时间



![image-20201029102238949](/Users/chenguanlin/Documents/note/trace and span with zip.png)

<center><b>how Span and Trace look in a system with the Zipkin annotations</b></center>

每种颜色就是一个span，类似过滤器链

他们之间的链路关系：

![image-20201029103103593](/Users/chenguanlin/Documents/note/sleuth link.png)



## 使用



**Distributed Tracing with Zipkin**

​		不需要自己构建Zipkin Server，只需要调用jar包即可，java -jar

​		当选择一个特定的跟踪时，会看到合并的span。这意味着，如果有两个span发送到Zipkin，其中服务器接收和服务器发送注释，或者客户端接收和客户端发送注释，它们将作为单个span呈现

查看错误：

​		当抛出一个异常而没有被捕获时，我们在span上设置适当的标记，然后Zipkin可以对其适当着色，点击红色的span查看原因。



**Distributed Tracing with Brave**

​		Spring Cloud Sleuth2.0开始，使用Brave作为the tracing library，因此，Sleuth不再负责存储上下文，而是将工作委托给Brave。

​		Sleuth与Brave的命名和标记习惯不同，默认遵循的是Brave的，也可以将spring.sleuth.http.legacy.enabled置为true来使用sleuth的。

​		

***日志聚合工具***







使用

1. 添加依赖

2. 主启动类

3. 配置文件

   ```yml
   apring:
    zipkin:
     # 去哪里查看
     base-url: http:localhost:9411
     sleuth:
      sampler: 
       pro
   
   ```




# 分布式事务











bootstrap中

* application.name
* eurekaClient
* config





@EnableEurekaClient只适用于Eureka作为注册中心，@EnableDiscoveryClient 可以是其他注册中心







