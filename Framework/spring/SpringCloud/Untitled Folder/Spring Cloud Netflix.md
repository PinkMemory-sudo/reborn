











# Eurake



## Service Discovery[: Eureka Clients](https://docs.spring.io/spring-cloud-netflix/docs/2.2.5.RELEASE/reference/html/#service-discovery-eureka-clients)



1. 引入Eureka Client

group ID：org.springframework.cloud

artifact ID ：spring-cloud-starter-netflix-eureka-client

Eureka Client就是eureka的一个instance，用来注册自己和查询注册，定位其他服务

2. 使用eureka注册服务

注册提供的自己的 meta-data，包括host, port, health indicator URL, home page, and other details.

Eureka接收每个服务实例的心跳消息，如果心跳超时，就会从注册表中删除这个实例。

引入依赖，配置文件中指出Eureka server的地址，

```yml
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
```

defaultZone属性区分大小写，并且需要驼峰大小写

默认的应用名称，虚拟主机，端口号：

${spring.application.name}, ${spring.application.name} and ${server.port}

instance的行为配置是eureka.instance.*，但是如果有spring.application.name，将会将应用名作为Eureka service ID 



关闭Eureka Discovery Client：

eureka.client.enabled=false

或spring.cloud.discovery.enabled=false



### [Authenticating with the Eureka Server](https://docs.spring.io/spring-cloud-netflix/docs/2.2.5.RELEASE/reference/html/#authenticating-with-the-eureka-server)

如果eureka.client.serviceUrl.defaultZone指定的Urls嵌入了凭证，基于Http的认证将自动嵌入eureka client

可以在指定eureka.client.serviceUrl.defaultZone时，可以使用user:password@localhost:8761/eureka这种样式来嵌入凭证。

跟复杂的，可以创建一个DiscoveryClientOptionalArgs的bean，将ClientFilter注入进它，这样设置客户机对服务器的调用时都使用。

但是有限制，它只支持第一组注册中心的校验。



当Eureka服务器需要客户端证书进行身份验证时，可以通过属性配置客户端证书和信任存储

```yml
eureka:
  client:
    tls:
      enabled: true
      key-store: <path-of-key-store>
      key-store-type: PKCS12
      key-store-password: <key-store-password>
      key-password: <key-password>
      trust-store: <path-of-trust-store>
      trust-store-type: PKCS12
      trust-store-password: <trust-store-password>
```

省略trust-store时将使用JVM default trust store

eureka.client.tls.key-store-type和eureka.client.tls.trust-store-type默认都是PKCS12

当密码属性忽略时，默认就假设密码为空



### actuator查看Eureka的状态和健康

页面路径  /info或者/health来查看

http://localhost:9100/actuator/health

路径可以更改

```yml
eureka:
  instance:
    statusPageUrlPath: ${server.servletPath}/info
    healthCheckUrlPath: ${server.servletPath}/health
```



### 通过HTTPS连接

需要设置两个标志

- `eureka.instance.[nonSecurePortEnabled]=[false]`
- `eureka.instance.[securePortEnabled]=[true]`



由于eureka的内部工作原理，the status and home pages仍然是http，除非在配置文件中显式的覆盖它

```yml
eureka:
  instance:
    statusPageUrl: https://${eureka.hostname}/info
    healthCheckUrl: https://${eureka.hostname}/health
    homePageUrl: https://${eureka.hostname}/
```



### [Eureka’s Health Checks](https://docs.spring.io/spring-cloud-netflix/docs/2.2.5.RELEASE/reference/html/#eurekas-health-checks)

默认通过心跳来判断，client成功注册后的状态是up

开启Eureka health checks，更改此行为，

```yml
eureka:
  client:
    healthcheck:
      enabled: true
```

只能在application.yml中设置，在bootstrap.yml设置，将造成不确定影响，如registering in Eureka with an `UNKNOWN` status



# Hystrix 

熔断器， 线程池



断路器计算何时打开和关闭电路，以及在失败的情况下做什么



导入Hystrix

group ID：org.springframework.cloud

 artifact ID: spring-cloud-starter-netflix-hystrix



启动类上添加@EnableCircuitBreaker



```java
@Component
public class StoreIntegration {

    @HystrixCommand(fallbackMethod = "defaultStores")
    public Object getStores(Map<String, Object> parameters) {
        //do stuff that might fail
    }

    public Object defaultStores(Map<String, Object> parameters) {
        return /* something useful */;
    }
}
```





Zuul

# Spring Cloud Netflix

集成了多个Netflix OSS组件，比如Eureka, Hystrix, Zuul, Archaius等



## Zuul

Zuul是Netflix提供的基于JVM的路由器和服务器端负载平衡器

映射，验证

Zuul的作用:

- Authentication
- Insights
- Stress Testing
- Canary Testing
- Dynamic Routing
- Service Migration
- Load Shedding
- Security
- Static Response handling
- Active/Active traffic management



The configuration property `zuul.max.host.connections` has been replaced by two new properties, `zuul.host.maxTotalConnections` and `zuul.host.maxPerRouteConnections`, which default to 200 and 20 respectively.



使用

1. 导入

group ID：org.springframework.cloud

artifact ID：spring-cloud-starter-netflix-zuu



***内嵌的Zuul反向代理***

Spring Cloud创建了一个嵌入式Zuul代理，以简化UI应用程序对一个或多个后端服务进行代理调用的通用用例的开发。

这个特性对于将用户界面代理到它需要的后端服务非常有用，从而避免了为所有后端独立管理CORS和身份验证问题的需要。



启动类添加@EnableZuulProxy开启它，这样做将导致本地调用被转发到适当的服务，

```yml
 zuul:
  ignoredServices: '*'
  routes:
    users: /myusers/**
```

id为users的服务接收来自位于/ users的代理的请求(前缀已经去除)，代理使用Ribbon定位要通过发现转发到的实例。

***可以理解成***  将**之前的路径替换成了对应的服务的host+port

即匹配到了忽略的服务，业匹配到的其他服务，不会被忽略



要对路由进行更细粒度的控制，可以分别指定path和serviceId

```yml
 zuul:
  routes:
    users:
      path: /myusers/**
      serviceId: users_service
```



后端位置可以指定为serviceId(用于发现服务)或url(用于物理位置)

```yml
 zuul:
  routes:
    users:
      path: /myusers/**
      url: https://example.com/users_service
```



































通过网关进行认证，负载均衡等

zuul的核心是一系列的**filters**, 其作用可以类比Servlet框架的Filter，或者AOP。

Zuul中定义了四种标准过滤器类型，这些过滤器类型对应于请求的典型生命周期

(1) PRE：这种过滤器在请求被路由之前调用。我们可利用这种过滤器实现身份验证、在集群中选择请求的微服务、记录调试信息等。

(2) ROUTING：这种过滤器将请求路由到微服务。这种过滤器用于构建发送给微服务的请求，并使用Apache HttpClient或Netfilx Ribbon请求微服务。

(3) POST：这种过滤器在路由到微服务以后执行。这种过滤器可用来为响应添加标准的HTTP Header、收集统计信息和指标、将响应从微服务发送给客户端等。

(4) ERROR：在其他阶段发生错误时执行该过滤器。



Config



server提供配置文件的存储、以接口的形式将配置文件的内容提供出去，client通过接口获取数据、并依据此数据初始化自己的应用

如果Git仓库为公开仓库，可以不填写用户名和密码，如果是私有仓库需要填写







