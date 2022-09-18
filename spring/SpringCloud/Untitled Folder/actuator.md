

Spring Boot包括许多附加特性，在应用程序上生产环境时监视和管理应用程序。

Spring Boot Actuator提供了SpringBoot的所有生产时特性，添加spring-boot-starter-actuator来开启



***Endpoints***

Spring Boot提供了很多端点(如health,info等），也支持自己添加端点

每个端点可以 enabled or disabled，通过HTTP或者JMX暴露端点，只有开启和暴露的端点才有用，内置的端点只有enable时才自动配置，大多数应用通过HTTP暴露端点，url映射成actuator/端点id



| ID                 | Description                                                  |
| :----------------- | :----------------------------------------------------------- |
| `auditevents`      | Exposes audit events information for the current application. Requires an `AuditEventRepository` bean. |
| `beans`            | Displays a complete list of all the Spring beans in your application. |
| `caches`           | Exposes available caches.                                    |
| `conditions`       | Shows the conditions that were evaluated on configuration and auto-configuration classes and the reasons why they did or did not match. |
| `configprops`      | Displays a collated list of all `@ConfigurationProperties`.  |
| `env`              | Exposes properties from Spring’s `ConfigurableEnvironment`.  |
| `flyway`           | Shows any Flyway database migrations that have been applied. Requires one or more `Flyway` beans. |
| `health`           | Shows application health information.                        |
| `httptrace`        | Displays HTTP trace information (by default, the last 100 HTTP request-response exchanges). Requires an `HttpTraceRepository` bean. |
| `info`             | Displays arbitrary application info.                         |
| `integrationgraph` | Shows the Spring Integration graph. Requires a dependency on `spring-integration-core`. |
| `loggers`          | Shows and modifies the configuration of loggers in the application. |
| `liquibase`        | Shows any Liquibase database migrations that have been applied. Requires one or more `Liquibase` beans. |
| `metrics`          | Shows ‘metrics’ information for the current application.     |
| `mappings`         | Displays a collated list of all `@RequestMapping` paths.     |
| `scheduledtasks`   | Displays the scheduled tasks in your application.            |
| `sessions`         | Allows retrieval and deletion of user sessions from a Spring Session-backed session store. Requires a Servlet-based web application using Spring Session. |
| `shutdown`         | Lets the application be gracefully shutdown. Disabled by default. |
| `threaddump`       | Performs a thread dump.                                      |

Web服务(Spring MVC, Spring WebFlux, or Jersey)还有

| ID           | Description                                                  |
| :----------- | :----------------------------------------------------------- |
| `heapdump`   | Returns an `hprof` heap dump file.                           |
| `jolokia`    | Exposes JMX beans over HTTP (when Jolokia is on the classpath, not available for WebFlux). Requires a dependency on `jolokia-core`. |
| `logfile`    | Returns the contents of the logfile (if `logging.file.name` or `logging.file.path` properties have been set). Supports the use of the HTTP `Range` header to retrieve part of the log file’s content. |
| `prometheus` | Exposes metrics in a format that can be scraped by a Prometheus server. Requires a dependency on `micrometer-registry-prometheus`. |



***开启端点***

`management.endpoint.端点id.enabled` =true 

默认除了shutdown的端点，其他端点都enable了。如果只想开几个，可以不用默认配置，单独写几个。

```yml
# 关闭默认，只开info
management.endpoints.enabled-by-default=false
management.endpoint.info.enabled=true
```



暴露端点：

默认暴露信息

| ID                 | JMX  | Web  |
| :----------------- | :--- | :--- |
| `auditevents`      | Yes  | No   |
| `beans`            | Yes  | No   |
| `caches`           | Yes  | No   |
| `conditions`       | Yes  | No   |
| `configprops`      | Yes  | No   |
| `env`              | Yes  | No   |
| `flyway`           | Yes  | No   |
| `health`           | Yes  | Yes  |
| `heapdump`         | N/A  | No   |
| `httptrace`        | Yes  | No   |
| `info`             | Yes  | Yes  |
| `integrationgraph` | Yes  | No   |
| `jolokia`          | N/A  | No   |
| `logfile`          | N/A  | No   |
| `loggers`          | Yes  | No   |
| `liquibase`        | Yes  | No   |
| `metrics`          | Yes  | No   |
| `mappings`         | Yes  | No   |
| `prometheus`       | N/A  | No   |
| `scheduledtasks`   | Yes  | No   |
| `sessions`         | Yes  | No   |
| `shutdown`         | Yes  | No   |
| `threaddump`       | Yes  | No   |



management.endpoints.web.exposure.include=ids

*表示暴露所有(除了env和beans)，

另外，*在yml中有特殊含义，所以赋值时应该加上双引号

```yml
management:
  endpoints:
    web:
      exposure:
        include: "*"
```



include和exclude中exclude优先级高

暴露端点后，我们应该 [secure your endpoints](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html#production-ready-endpoints-security)

如果想在端点被公开时实现自己的策略，可以注册一个EndpointFilter bean。



## 配置endpoints

没有带任何参数的读取操作，Endpoints会自动缓存它的response

设置一个endpoint的缓存存活时间：

```properties
management.endpoint.beans.cache.time-to-live=10s
```

beans端点的缓存存活10s

## Actuator Web Endpoints 的发现页

默认是/actuator，可以自定义management context path，但是如果将路径配置为/，则会禁用发现页，因为很容易发生冲突

## CORS Support

默认关闭CORS，设置management.endpoints.web.cors.allowed-origins属性才能开启，



使用：

1. 添加依赖

   ```xml
   <dependencies>
       <dependency>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-actuator</artifactId>
       </dependency>
   </dependencies>
   ```

2. 







自定义端点路径

通过**management.endpoints.web.base-path**=/manage































































`actuator`是`spring boot`项目中非常强大一个功能，有助于对应用程序进行监视和管理，通过 `restful api` 请求来监管、审计、收集应用的运行情况，针对微服务而言它是必不可少的一个环节







