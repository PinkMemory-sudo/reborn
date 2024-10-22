服务的所有功能通过接口的方式暴露给其他服务。OpenFeign相当于一个声明式的HTTP客户端，等效于RestTemplate+Ribbon.



服务接口的调用

Feign已经停止更新

替换方案：OpenFeign

消息转换器

接口上加注解实现Http客户端



？

* 参数的传递
* 结果的转换





# 使用



1. 加依赖

```xml
<dependency>
  <groupId>org.springframework.cloud</groupId>
  <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

2. 启动类上加注解

   `@EnableFeignClients`

3. 定义接口

   服务提供者通过Controller层的接口对外提供功能

   服务消费者可以通过接口+注解的方式，访问服务提供者的Controller

服务提供者provider01的Controller

```java
@GetMapping("/hello")
    public String hello() {
        return name;
    }
```

服务消费者的接口，接口用@FeignClient指定要使用的服务名，方法上添加Mapping指定要调用的接口。

```java
@FeignClient("provider01")
public interface Hello {

    @GetMapping("/hello") //请求结果作为接口的返回值
    String hello();
}
```

**这样，在使用其他服务的功能时，就想调用自己的一个接口一样**，面向接口编程，还是通过接口去掉service。



## **超时控制**

openFeign默认的超时时间是1s，但是某些接口确实耗时，超时控制由ribbon设置

配置文件中设置建立连接时间和连接后读取资源超时的时间

```properties
ribbon.ReadTimeout=2000
ribbon.ConnectTimeout=2000
```



## **OpenFeign日志**

如果需要查看OpenFeign调用的细节，返回值细节时如何配置



**添加配置类**

```java
@Configuration
public class FeignConf {

    @Bean
    public Logger.Level lever() {
        return Logger.Level.FULL;
    }
}
```

日志级别

| 级别   | 描述                                            |
| ------ | ----------------------------------------------- |
| NONE   | 默认，不显示任何日志                            |
| BASIC  | 仅记录请求方式，url，状态码，响应时间等基础信息 |
| HEADER | 基础信息+请求和响应头信息                       |
| FULL   | 全部                                            |



**配置文件中设置监控的日志级别**

```properties
logging.level.com.pk.consumer8021.controler.Test=debug
```



上述配置表示Test接口中以debug形式打印feign调用的full日志





