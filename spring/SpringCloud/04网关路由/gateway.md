# 概念



**网关用来做什么**

分布式的入口，不暴露内部服务。基于Filter实现，日志，限流，权限安全等工作。

<img src="..\imgs\网关.png" alt="1646532892154" style="zoom: 80%;" />

**相比zuul的优势**

* 基于异步非阻塞模型
* 集成了Circuit Breaker和Spring Cloud DiscoveryClient
* 可以对路由进行Predicate和Filter



**工作流程**

![1646533650933](..\imgs\SpringColoud工作流程.png)

* 客户端向SpringCloud Gateway发送请求

* Gateway Handler Mapping将请求映射到与请求匹配的Gateway Web Handle
* Handle通过指定的过滤器链将请求发送到微服务然后返回
* 在过滤器链中可以对请求进行修改
* pre过滤前可以进行参数校验，鉴权，流量监控，日志输出，协议转换等
* post,过滤后可以修改响应，响应头，等



**三大核心概念**

路由负责定位真正的服务节点，断言负责判断要不要转发，过滤器可以在转发前后做一些修改。

***路由***

Path映射到url，通过匹配条件，定位到真正的服务节点

***断言***

添加一些匹配条件，是Java8的Predicate，输入是Spring Framework ServerWebExchange，用来匹配HTTP的request，如请求头和参数等

***过滤器***

拦截器

在请求被路由前后对请求进行修改

Spring Framework GatewayFilter的实例

can modify requests and responses before or after



# 使用

1. 配置文件中配置
2. Java代码



**注意**

Spring Cloud Gateway is built on Spring Boot 2.x, Spring WebFlux, and Project Reactor. As a consequence, many of the familiar synchronous libraries (Spring Data and Spring Security, for example) and patterns you know may not apply when you use Spring Cloud Gateway

it does not work in a traditional Servlet Container or when built as a WAR.

需要一处WEB的依赖



1. 添加依赖



2. 配置文件

网关也需要注册到服务中心，网关中没有业务类

3. 启动类加注解@EnableEurekaClient



## 2. 注入RouteLocator

```java
@Bean
    public RouteLocator remoteRoutes(RouteLocatorBuilder builder) throws Exception {
        return builder.routes().route("id",p -> p
                // 匹配哪个路径
                .path("/**")
                // 转发到哪
                .uri("https://www.cnblogs.com")
                ).build();
    }
```

route两种重载来添加路由：

`route(Function<PredicateSpec, AsyncBuilder> fn)`

`route(String id, Function<PredicateSpec, AsyncBuilder> fn)`



**通过微服务名实现动态路由**

引入后不想使用可以用`spring.cloud.gateway.enabled=false` 关闭











































