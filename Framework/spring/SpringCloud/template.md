# Spring Cloud Gateway 网关路由

1. 添加依赖

   ```xml
   <dependency>
     <groupId>org.springframework.cloud</groupId>
     <artifactId>spring-cloud-starter-gateway</artifactId>
   </dependency>
   ```

   

2. 启动类上加注解

   ```java
   @EnableEurekaServer
   ```

3. 配置文件

   ```yml
   spring:
     cloud:
       gateway:
       	# 开启服务发现，可以用服务名代替uri
         discovery:
           locator:
             enabled: true
         routes:
           - id: multi_route
             uri: lb://CONSUMER
             # 一组断言: 请求方法为get，路径为/hello/**,
             predicates:
               - Method=GET
               - Path=/user/**
               - After=2017-01-20T17:42:47.789-07:00[America/Denver]
   ```



熔断

降级

限流



# Eureka Server 



## server

1. 添加依赖

   ```xml
   <dependency>
     <groupId>org.springframework.cloud</groupId>
     <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
   </dependency>
   ```

   

2. 启动类上加注解

   @EnableEurekaServer

3. 配置文件

   ```yml
   eureka: 
     client: 
     # 注册自己吗
      register-with-eureka: false
     # 是否从eureka拉取注册信息
      fetch-registry: false
     # 提供的访问地址
      serviceUrl: 
       defaultZone: http://localhost:${server.port}/eureka/
   ```



## Client

1. 添加依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
   </dependency>
   ```

2. 启动类上加注解

   @EnableEurekaClient

3. 配置文件

   ```yml
   eureka:
     client:
       serviceUrl:
        # 注册中心的地址，可以用逗号隔开
         defaultZone: http://localhost:8000/eureka/
   ```



# OpenFiegn



## 服务调用

1. 添加依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-openfeign</artifactId>
   </dependency>
   ```

2. 启动类上加注解

   @EnableFeignClients

3. 配置文件

   ```yml
   feign:
     client:
       config:
         default:
           connectTimeout: 5000
           readTimeout: 5000
           loggerLevel: full
   ```

Openfiegn简化了服务调用，只需要写接口，加注解就可以。

```java
// 指定要调用的服务名
@FeignClient(name = "provider")
public interface HelloRemote {
    // 拼接上请求地址发送请求，请求结果作为返回值
    @GetMapping("/hello")
    String hello();
}
```

采用动态代理，其他类可以自动导入

## 容错

​	Feign支持hsytrix，禁用hsytrix可以创建一个简单的假功能,“原型”范围

```java
@Configuration
public class FooConfiguration {
        @Bean
    @Scope("prototype")
    public Feign.Builder feignBuilder() {
        return Feign.builder();
    }
}
```

开启容错

```yml
#开启容错
feign:
  hystrix:
    enabled: true
  client:
    config:
      default:
        # 设置请求超时时间，该时间内请求失败不会降级，会进行重试
        connectTimeout: 5000
        readTimeout: 5000
        # 日志全部记录
        loggerLevel: full
```



## 服务降级

Feign Hystrix Fallbacks，创建一个实现类，使用@FeignCilent的fallback实行指定实现，将在服务出错时执行本地实现。

Hystrix默认的超时时间是1秒，如果超过这个时间尚未响应，将会进入fallback代码

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



如果需要触发降级的原因，实现类实现被**FallbackFactory**<T>泛型是服务接口名，@FeignCilent的fallback中指定fallbackFactory

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
          // 实现服务降级，可以在cause中获得降级原因
            @Override
            public Hello iFailSometimes() {
                return new Hello("fallback; reason was: " + cause.getMessage());
            }
        };
    }
}
```

***注意***

* 方法返回值是com.netflix.hystrix.HystrixCommand` and `rx.Observable目前不支持降级
* 使用hsytrix进行服务降级，可能有多个实现，将会导致@Autowired不能工作，Spring Cloud OpenFeign将所有Feign的实例都@Primary，有多个实现类，@Primary只能有一个，可以将@FeignClient的primary属性设为false
* @FeignClient的可以继承一个接口，不建议在服务器和客户机之间共享接口

[Feign request/response compression](https://docs.spring.io/spring-cloud-openfeign/docs/2.2.5.RELEASE/reference/html/#feign-requestresponse-compression)

[Feign logging](https://docs.spring.io/spring-cloud-openfeign/docs/2.2.5.RELEASE/reference/html/#feign-logging)



## 负载均衡

Fiegn集成了Ribbon，默认的负载均衡策略是轮询

Ribbon提供了其中负载平衡算法，都继承有AbstractLoadBalancerRule。

子类RoundRobinRule有两种子类：

* RandomRule：随机
* RoundRobinRule：轮询，默认的
* RetryRule：规定时间内，调用不成功会重试，默认开启，只需要设置超时时间

* ResponseTimeWeightedRule，根据响应时间加权，过时，使用WeightedResponseTimeRule替代

* WeightedResponseTimeRule，根据响应时间加权
* BestAvailableRule：最低并发策略，通常ServerListSubsetFilter
* ZoneAvoidanceRule：根据server所在区域的性能，和server的可用性，轮询选择server
* AvailabilityFilteringRule：可用过滤，过滤掉断开的，连接数超过阈值的



### 切换负载均衡策略

#### 针对某一个服务

1. 创建实现负载均衡的类，不能被componentScan扫描到，否则就会改变所有的

```java
public class RibbonLB {
   // 使用@Bean注入一个ribbon提供的实现类的实例
    @Bean
    public IRule randomRule() {
        return new RandomRule();
    }
}
```

2. 将这个策略应用到某个FiegnClient：启动类上添加注解@RibbonClient(name = "用于请求那个服务(名)", configuration = {RibbonLB.class})

或者使用配置文件指定

```yml
服务名:
  ribbon:
    NFLoadBalancerRuleClassName: 提供策略的权限定类名
```



#### 切换所有

与上面的相似，实现类加@Configuration和@Bean变成配置类，放到能被ComponentScan扫描到的部分



### 自定义负载均衡

 与上边相似





## @FiegnCilent的其他属性

*  configuration ：指定Feign配置类，可以自定义Feign的Encoder、Decoder、LogLevel、Contract
* name：指定FeignClient的名称
* url: url一般用于调试，可以手动指定@FeignClient调用的地址
* decode404:当发生http 404错误时，如果该字段位true，会调用decoder进行解码，否则抛出FeignException
* path: 定义当前FeignClient的统一前缀



# SpringCloud Config

实现服务配置的集中化管理，server端从外部配置中心获取属性，client从server获得



## server端

默认使用git存储，也可以使用本地文件存储配置

1. 添加依赖
2. 启动类上加注解 @EnableConfigServer
3. 配置文件

***本地存储***

```properties
# 激活本地存储
spring.profiles.active=native
# 指定配置文件存储路径
spring.cloud.config.server.native.searchLocations = file:D:/properties/
```

***git***

```yml
spring:  
 cloud:
    config:
      server:
        git:
          uri: https://github.com/PinkMemory-sudo/config-demon #配置文件所在仓库
          default-label: main #配置文件分支
          search-paths: config  #配置文件所在根目录
          # 私有库还需要设置username和password
```

配置完成后，可以通过/分支名/文件名来查看具体的配置文件，还有其他的方法

然后他会将文件存储到本地：

![image-20201028132228434](/Users/chenguanlin/Documents/note/0img/配置中心获得属性文件的日志信息.png)



## client端

1. 添加依赖

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-starter-config</artifactId>
   </dependency>
   ```

2. 启动类上不用加注解

3. 配置文件

   需要在服务启动前先讲服务注册到注册中心，启动成功后从注册中心获取配置

   ***bootstrap.yml***

   ```yml
   spring:
     application:
       name: config-test-consumer
     cloud:
       # 设置配置中心uri以及要获得的哪个分支的什么配置文件
       config:
         discovery:
           # 开启服务发现，查找服务，默认是false
           enabled: true
           # 开启服务发现后，可以用服务名代替uri
           service-id: CONFIG-SERVER-CENTER
   #      uri: http://localhost:8040
         label: main # 分支名
         name: consumer # 文件名(去掉-dev等)
         profile: dev # 文件名后面代表哪种环境dev/test/prod/beta/release
   
   eureka:
     client:
       service-url:
         defaultZone: http://localhost:8000/eureka
   ```



## 加密/解密



## 手动刷新配置文件

更改外部的配置文件，通过配置服务中心访问会得到新的配置文件，但是客户端的配置初始化时已经加载和解析了，此时的配置文件还是之前的，重启服务刷新配置又不方便，客户端可以对外暴露一个刷新接口，通过调用刷新接口来刷新配置文件

1. 添加依赖

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-actuator</artifactId>
   </dependency>
   ```

2. 需要刷新的类上加注解@RefreshScope

3. 配置文件

   ```yml
   management:
     endpoints:
       web:
         exposure:
         	# 暴露refresh, 默认只暴露了helth和info
           include: include: refresh, info, health  
   ```

4. 调用接口刷新配置文件  /actuator/refresh  (POST请求)，如果配置文件没有更改，返回[]，又修改时会返回一个数组，包含所有配修改的配置名。
5. 可以写个脚本，调用要刷新的接口

# SpringCloud Bus



使用Spring Cloud Config能实现服务配置的集中化管理，如果将某个配置属性进行修改，需要手动调用接口去刷新

Spring Cloud Bus可以看成是SpringCloud的消息总线。

通过消息中间件(默认RabbitMQ)将各个节点连接起来，实现广播状态的更新等。  

目前提供了两种类型的消息队列中间件支持：RabbitMQ与Kafka（对应的pom依赖分别为spring-cloud-starter-bus-amqp， spring-cloud-starter-bus-kafka）





## 使用Spring Cloud Config实现配置的动态刷新



Spring Cloud Bus，就像是一个分布式的Spring Boot Actuator,提供了两个post接口：

* /actuator/bus-env：使用键值对更新***每个实例***的Environment，默认不暴露，需配置management.endpoints.web.exposure.include=bus-env 来开放接口访问

* /actuator/bus-refresh：清空***RefreshScope***缓存，重新绑定@ConfigurationProperties， 默认不暴露，可通过配置management.endpoints.web.exposure.include=bus-refresh 来开放接口访问

两者区别

```java
@RestController
@RefreshScope
public class HelloController {

    @Autowired
    private Environment env;

    @Value("${app}")
    private String app;

    @RequestMapping("/hello")
    public String hello(){
        return "Hello, welcome to spring cloud 2. env: " + env.getProperty("app") + ", value: " + app;
    }
}
```

调用bus-env，Environment的值会刷新

调用bus-refresh，@Value的值会刷新



![image-20201028142106926](/Users/chenguanlin/Documents/note/0img/bus更新配置文件.png)

1. 修改配置文件后push到远程仓库
2. 调用接口通知配置服务器
3. config作为消息生产者，收到跟新时发送消息到bus

4. bus通知服务，
5. 服务再从配置服务器上拉取最新配置刷新
6. 配置服务器从远程仓库拉取最新配置



***使用***

1. 添加依赖， 配置服务器和客户端添加

   ```xml
   <dependency>
       <groupId>org.springframework.cloud</groupId>
       <artifactId>spring-cloud-bus</artifactId>
   </dependency>
   <!--创建项目时选择了cloud-bus，不要忘了引入amqp,bus需要消息中间件-->
   <dependency>
     <groupId>org.springframework.cloud</groupId>
     <artifactId>spring-cloud-starter-bus-amqp</artifactId>
   </dependency>
   ```

2. 启动类上不加

3. 配置文件

   服务端

   ```yml
   # 配置rabbitMQ
   spring:
     rabbitmq:
       host: 127.0.0.1
       port: 5672
       # 默认的用户名和密码就是guest
       username: guest
       password: guest
   # 暴露端口
   management:
     endpoints:
       web:
         exposure:
           clude: bus-env,bus-refresh
   ```

   客户端

   ```yml
   spring:
     application:
       name: config-bus
     cloud:
       config:
         discovery:
           enabled: true
           service-id: CONFIG-SERVER-CENTER
         label: main
         profile: dev
         name: consumer
     rabbitmq:
       host: localhost
       port: 5672
       # 用户名和密码使用默认的guest
   
   eureka:
     client:
       service-url:
         defaultZone: http://localhost:8000/eureka/
   ```

访问服务端的bus-env或bus-refresh来刷新所有服务



# Spring Cloud Sleuth

一个请求可能需要调用很多个服务，出错时难以定位，引出了服务链路追踪。

Spring Cloud Sleuth中集成Twitter的Zipkin





