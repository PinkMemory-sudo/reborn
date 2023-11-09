

# Spring



## IOC



**什么是控制反转**

IoC(Inverse of Control:控制反转)是一种设计思想，就是将原本在程序中手动创建对象的控制权，交由**Spring**框架来管理。将对象之间的相互依赖关系交给 IoC 容器来管理，并由 IoC 容器完成对象的注入。这样可以很大程度上简化应用的开发，把应用从复杂的依赖关系中解放出来。



**什么是IOC容器**

IoC 容器是 Spring 用来实现 IoC 的载体， IoC 容器实际上就是个Map（key，value）,Map 中存放的是各种对象



**IOC容器初始化过程**



**Spring 中 beanFactory 和 ApplicationContext 的联系和区别**

ApplicationContext是ApplicationContext的子类，可以实现BeanFactory 所有可实现的功能，还具备其他更多的功能。

ApplicationContext预先加载,beanFactory懒加载



**什么是依赖注入依赖注入有几种方法,区别**

SpringBoot都是通过注解注入，Spring有构造注入，setter注入





- **工厂设计模式** : Spring使用工厂模式通过 `BeanFactory`、`ApplicationContext` 创建 bean 对象。
- **代理设计模式** : Spring AOP 功能的实现。
- **模板模式**：jdbcTemplate，MongoTemplate
- **单例设计模式** : Spring 中的 Bean 默认都是单例的。
- **包装器设计模式** : 我们的项目需要连接多个数据库，而且不同的客户在每次访问中根据需要会去访问不同的数据库。这种模式让我们可以根据客户的需求能够动态切换不同的数据源。
- **观察者模式:** Spring 事件驱动模型就是观察者模式很经典的一个应用。
- **适配器模式** :Spring AOP 的增强或通知(Advice)使用到了适配器模式、spring MVC 中也是用到了适配器模式适配`Controller`。



**BeanFactory与FactoryBean**

每个Bean对应一个FactoryBean



**bean的作用域**

| 作用域         | 描述                                                    |
| -------------- | ------------------------------------------------------- |
| singleton      | bean 默认都是单例的                                     |
| prototype      | IOC容器可以创建多个Bean实例，每次返回的都是一个新的实例 |
| request        | 每个请求创建一个                                        |
| session        | 每个会话创建一个                                        |
| global-session | 所有的Session共享一个Bean实例，Spring5已经没有了        |



**Bean生命周期**

大流程：实例化，依赖注入，初始化，销毁

扩展点：BeanNameAware，BeanFactoryAware，BeanPostProcesser



实例化

创建Bean

通过反射调用setter方法对属性进行赋值

Aware：如果实现类BeanNameAware，设置Bean的Name，如果实现了BeanFactory，就设置Factory

PostProcessor：

InitializingBean：

PostProcessor：

放入Map中使用

容器关闭时DisposableBean：





**单例bean是否有线程安全问题，怎么保证线程安全**

* bean中尽量不要成员变量
* 使用ThreadLocal



**SpringBean是线程安全的吗**

单例的，但不是线程安全的



**@Component 和 @Bean 的区别是什么**

* @Component 注解作用于类，而 @Bean 注解作用于方法。
* @Component通过@ComponentScan扫描自动装配，@Bean方法返回一个bean，与@Configuration一起使用



## AOP



**什么是AOP**

面向切面编程，通过预编译和运行时动态代理来实现在不修改源码的情况下，给程序统一添加功能的技术。可以将非业务代码统一提取出来处理(例如事务处理、日志管理、权限控制等)封装起来，做横向的扩展，跟业务代码分离，减少重复代码和耦合度。



**AOP的名词**

连接点：程序的某一个位置，如方法执行前

切入点：AOP增强执行的地方，如某个方法执行前

切面：增强的集合

增强：非业务代码



**AOP都有哪些实现**

Spring AOP就是基于动态代理的，如果要代理的对象，实现了某个接口，那么Spring AOP会使 用JDK Proxy，去创建代理对象，而对于没有实现接口的对象，就无法使用 JDK Proxy 去进行代 理了，这时候Spring AOP会使用Cglib。

我们配置了切面后，Spring动态生成代理类，后续我们使用到的对象就是动态代理类的实例。



**静态代理和动态代理的区别**

静态：要和代理的对象实现一样的接口，编写代理类，代理类在编译时生成，将增强织入到字节码文件中。

动态：代理类在程序运行时运用反射机临时创建



**JDK代理与Cglib代理的区别**

* JDK代理需要代理类实现接口，利用拦截器（必须实现InvocationHandler）加上反射机制生成一个代理接口的匿名类，在调用具体方法前调用InvokeHandler来处理
* Cglib是针对类实现代理，主要是对指定的类生成一个子类，覆盖其中的方法，并覆盖其中方法的增强



**SpringAOP和AspectJAOP的区别**

SpringAOP是运行时增强，AspectJ是编译时增强。 

Spring AOP 基于代理(Proxying)，而 AspectJ 基于字节码操作(Bytecode Manipulation)。

Spring AOP 已经集成了 AspectJ，AspectJ 相比于 Spring AOP 功能更加强大，但是 Spring AOP 相对来说更简单。但是，当切面太多的话，最好选择 AspectJ ，它比Spring AOP 快很多。



**SpringAOP使用**

1. 使用表达式(* 权限定类名.方法名(..))
2. @annotation 有某注解的



**如何处理控制器层的异常**

@ControllerAdvice可以与`@ExceptionHandler` 等注解注解配套使用

@ControllerAdvice可以进行全局的异常处理；

创建一个类，类上添加注解@ControllerAdvice，然后在该类中写处理异常的方法，在方法上添加@ExceptionHandler(XXX.class)指定异常的类型



**代理失效**





## 依赖注入

把对象依赖的属性注入到对象中，有两种方式：构造器注入和属性注入



**怎么解决循环依赖问题**

对于构造器注入，Spring处理不了，对于属性注入，Spring是通过三级缓存来实现的。







## 事务



**Spring的事务传播机制**

Service接口方法可能会在内部调用其它的Service接口方法以共同完成一个完整的业务操作，因此就会产生服务接口方法嵌套调用的情况， Spring通过事务传播行为控制当前的事务如何传播到被嵌套调用的目标服务接口方法中。即当前的事务(调用的方法)遇到了另一个事务(方法调用了另一个方法)，另一个事务怎么办。

分为支持当前事务和不支持当前事务

| 传播行为                  | 描述                                                         |
| ------------------------- | ------------------------------------------------------------ |
| REQUIRED                  | 默认的，如果当前没有事务，就新建一个事务，如果有，就加入当前事务 |
| PROPAGATION_SUPPORTS      | 以当前事务为准，当前没有事务，就以非事务的方式运行           |
| PROPAGATION_MANDATORY     | 以当前事务为准，当前没有事务，就抛出异常                     |
| PROPAGATION_REQUIRES_NEW  | 新建事务，把当前事务挂起                                     |
| PROPAGATION_NOT_SUPPORTED | 不要事务，把当前事务挂起                                     |
| PROPAGATION_NEVER         | 非事务方式运行，当前存在事务时抛出异常                       |
| NESTED                    | 与REQUIRED类似，只不过它是嵌套的事务，嵌套事务出错是不会全部回滚 |



**@Transactional**

加上`rollbackFor=Exception.class`,可以让事物在遇到非运行时异常时也回滚



**事务失效的原因**

1. @Transactional没有放在public方法上，Spring Framework 默认使用 AOP 代理，在代码运行时生成一个代理对象来执行事务，而直接通过service调用方法，执行的不是代理对象，所以索引失效了。
2. **在同一个类之中，方法互相调用，切面无效**

总结一句话，**调用了自身而没有经过 Spring 的代理类**

解决方案：事务代码用一个类单独去处理

2. 异常被吃掉了
3. 数据库不支持事务
4. bean没有注入





## 热点

**Spring中的bean生命周期**

[参考](https://www.cnblogs.com/javazhiyin/p/10905294.html)

[参考](https://snailclimb.gitee.io/javaguide-interview/#/./docs/e-1spring)

* 容器找到配置文件中对Bean的定义

* 利用Java的反射创建一个Bean实例

* 如果Bean实现了BeanNameAware,调用setBeanName设置Bean的名字

* 如果Bean实现了BeanClassLoaderAware，调用setBeanClassLoader()方法，传入 ClassLoader对象的实例

* 实现了*.Aware接口，就调用响应的方法




**BeanFactory和FactoryBean的区别**

BeanFactory是管理Bean的容器



**@Autowired和@Resource的区别**

@Autowired是Spring的注解，默认按照类型进行注入，有多个实例时，可以使用@Qualifier执行名字

@Resource是Java的注解，默认找名字进行注入



**@Async有什么要注意的**

原理：代理模式，使用新线程去执行方法

1. 最好配置一个线程池，否则使用默认的SimpleAsyncTaskExecutor，每次都会创建一个线程
2. 调用本类的方法是不会起作用的，因为绕过了代理



# SpringMVC



**MVC是什么**

MVC设计思想是将应用分为业务模型，视图，控制器三层，目的是实现前端页面的展现和后端数据的处理之间分离

M表示业务处理

V表示前端

C表示接收请求调用M的处理器



**工作流程**

1. 发送请求到**DispatchServlet**，DispatchServlet调用**HandlerMapping**通过url找到对应的处理器及拦截器生成执行链返回给DispatchServlet
2. DispatchServlet调用**HandlerAdapter**，HandlerAdapter进行参数校验，格式转换等后执行Handler，返回ModelAndView
3. DispatchServlet将ModelAndView调用**View Resoler**,
4. DispatchServlet将结果返回用户



**SpringMVC的控制器是不是单例模式，有什么问题，怎么解决**

是单例模式，这样在高并发的情况下等节约资源。

如果控制器内定义了很多属性可能会出现竞争问题，做法是不要在控制器中定义成员变量



**拦截器和过滤器的区别**





# SpringBoot





## 概念



**什么是SpringBoot**

Springboot是一个微服务框架，简化创建Spring的配置，springboot提供了许多自动配置



## 配置



**自动配置原理**

自动配置：自动将一些Bean注入到Spring容器

**@SpringBootApplication**由三个注解组成：@EnableAutoConfiguration，@ComponentScan，SpringBootConfiguration组成

```java
@SpringBootConfiguration //标注该类为配置类
@EnableAutoConfiguration //启动自动配置功能，Spring 中有很多以 Enable 开头的注解，其作用就是借助 @Import 来收集并注册特定场景相关的Bean ，并加载到 IOC 容器。
@ComponentScan // 扫描
```

```java
遍历整个ClassLoader中所有jar包下spring.factories文件。
spring.factories里面保存着springboot的默认提供的自动配置类
```



配置文件优先级



**bootstrap.yml与application.yml的区别**

bootstrap.yml用来加载远程配置文件，优先于application，一般在SpringCloudConfig或者Nacos中用到。

bootstrap主要用于从额外的资源来加载配置信息，



**@Value原理**





**profiles**



**如何将配置文件中的属性赋值给静态变量**



**排除一些程序包**



**禁用一些自动配置**



**替换Tomcat**



**不占用web端口**

```
spring.main.web-application-type=none
```





## 部署



**将SpringBoot部署到不同类型的服务器**



**什么是JavaConfig**

使用Java代码来注入配置

1. 面向对象，可以使用继承等特性进行配置
2. 减少XML的配置



**SpringBoot打成的jar包和普通Java的jar包的区别**

SpringBoot的jar无法被其他项目依赖，因为他们的结构不同



## 日志



**SpringBoot支持哪些日志框架，默认的日志框架是什么**







## 事务



**Spring中事务的隔离级别**

| 事务             | 说明                         |
| ---------------- | ---------------------------- |
| DEFAULT          | 使用后端数据库默认的隔离级别 |
| READ_UNCOMMITTED | 读未提交                     |
| READ_COMMITTED   | 读已提交                     |
| REPEATABLE_READ  | 可重读                       |
| SERIALIZABLE     | 串行读                       |



**事务传播机制**

Service接口方法可能会在内部调用其它的Service接口方法以共同完成一个完整的业务操作，因此就会产生服务接口方法嵌套调用的情况， Spring通过事务传播行为控制当前的事务如何传播到被嵌套调用的目标服务接口方法中。即当前的事务(调用的方法)遇到了另一个事务(方法调用了另一个方法)，另一个事务怎么办。

| 传播行为                  | 描述                                                         |
| ------------------------- | ------------------------------------------------------------ |
| REQUIRED                  | 默认的，如果当前没有事务，就新建一个事务，如果有，就加入当前事务 |
| PROPAGATION_SUPPORTS      | 以当前事务为准，当前没有事务，就以非事务的方式运行           |
| PROPAGATION_MANDATORY     | 以当前事务为准，当前没有事务，就抛出异常                     |
| PROPAGATION_REQUIRES_NEW  | 新建事务，把当前事务挂起                                     |
| PROPAGATION_NOT_SUPPORTED | 不要事务，把当前事务挂起                                     |
| PROPAGATION_NEVER         | 非事务方式运行，当前存在事务时抛出异常                       |
| NESTED                    | 与REQUIRED类似，只不过它是嵌套的事务，嵌套事务出错是不会全部回滚 |



**@Transactional**

加上`rollbackFor=Exception.class`,可以让事物在遇到非运行时异常时也回滚



**事务失效的原因**

1. @Transactional加在了方法上，Spring Framework 默认使用 AOP 代理，在代码运行时生成一个代理对象来执行事务，而直接通过service调用方法，执行的不是代理对象，所以索引失效了。

总结一句话，**调用了自身而没有经过 Spring 的代理类**

解决方案：事务代码用一个类单独去处理

2. 异常被吃掉了



**多数据源如何配置**



**多数据源的事务**



**微服务同时调多个接口，怎么使用事务**

分布式事务，消息的补偿机制



**消息补偿机制**



## 注解



**SpringBoot的核心注解，主要有哪几个注解组成**

@SpringBootApplication，主要由@SpringBootConfiguration，@EnableAutoConfiguration，@ComponentScan三个注解组成

@SpringBootConfiguration：等同于@Configuration,说明它是一个配置类，可以用来注入bean

@ComponentScan：自动扫描本包和子包路径下的 @Component(以及@Service等) 注解进行注册 bean 实例到 context 中

@EnableAutoConfiguration：开启自动配置，通过@Import，根据有什么依赖判断开启某个功能，注入到Spring容器，导入META-INF/spring.factories里自定义的配置类。每个自动配置类从xxxProperties.java读取配置





**@Required有什么用**

注入时是否必须存在，允不允许null



**@Qualifier**

有两个类实现了同一个接口，Spring不知道该注入哪一个，通过@Qualifier指定名字，确定到底注入哪一个，通常与@autowired连用



**@Primary**

自动装配时当出现多个Bean候选者时，被注解为@Primary的Bean将作为首选者，否则将抛出异常



**Spring注解@Resource和@Autowired区别对比**

@Resource并不是Spring的注解，是javax.annotation.Resource的，但是Spring支持该注解的注入

@Autowired是按照类型进行注入的

@Resource默认按名称装配，如果不到与名称匹配的bean，会按类型装配



**@Lazy(true)**

延迟初始化，@Lazy注解注解的作用主要是减少springIOC容器启动的加载时间



**@PostConstruct**

被注解的方法，会在所在对象加载完依赖注入后执行



**@PreDestroy**

关闭容器销毁对象时会执行



**@Component, @Controller, @Repository, @Service 有何区别**



**关于缓存的注解**







## 依赖配置



**SpringBoot自动配置原理**

通过判断类路径下有没有相应配置的jar包来确定是否加载和配置这个功能，读取jar包下的配置文件，将需要的bean注入到spring容器中



**SpringBootStarter的工作原理**



**spring-boot-starter-parent的作用**

也是一个pom文件

定义子标签的依赖版本号，所以我们不需要在写版本号

可以自定义，在dependencyManagement定义版本号，子标签就不用定义了

定义编码为utf-8

定义编译期版本为utf-8



**过滤程序包**



**多数据源配置**



**排除不需要的自动配置**



**启动SpringBoot后运行一些代码**

* ApplicationRunner接口
* CommandLineRunner接口



**日志框架**



**SpringBoot指定运行环境/配置文件**

创建多个配置文件:`application.-{profile}.preperties`,application.properties文件中通过spring.profiles.active属性来设置profile



**什么是JavaConfig**

用Java来配置spring容器，减少/消除xml配置。



**SpringBoot配置加载顺序**

properties, yml,系统环境变量，命令行参数

类路径下的，类路径的config下，jar包同级的，jar包同级目录config下的



**SpringBoot读取配置的方式**

@Value用来获得单个值

@ConfigurationProperties，有时候需要读取很多值，可以分组，根据前缀取映射到字段，前缀后的和属性名一致时自动映射

@PropertySource，配置信息不一定都放在application中，使用PropertySource可以指定要读取那个配置文件，可以与@Value和@ConfigrationProperties组合使用(yml暂不支持读取自定义的配置文件)



## **定时任务**



## 参数校验



## SpEL



## 事件



**拦截器过滤器监听器**

过滤器：Filter，init在启动时执行，doFilter在请求前后执行，destroy在容器销毁时执行

拦截器：一个Spring组件，并由Spring容器管理，并不依赖Tomcat等容器，是可以单独使用的Interceptor,preHandle决定

监听器：定义一个时间，触发时间，时间监听器去执行响应操作



1. 定义事件，就是定义一个类，封装事件的方法和属性
2. 定义事件监听器
3. 创建时间，通过ApplicationEventPublisher发布时间

事件

```java
public class MyEvent extends ApplicationEvent {

    public MyEvent(Object source) {
        super(source);
    }
}
```

事件监听器

```java
    @EventListener
    public void handleDemoEvent(MyEvent event) {
        log.info("调用的接口为:{}", event.getSource());
    }
```

发布事件

```java
@Aspect
@Component
@Slf4j
public class ApiStatAspect {

    @Autowired
    protected ApplicationEventPublisher eventPublisher;

    @Pointcut("@annotation(com.pk.springboot.annotation.ApiStat)")
    private void apiPoint(){}

    @Before("apiPoint()")
    public void addApiStat(){
        // 发布事件
        eventPublisher.publishEvent(new MyEvent("hello"));
    }
}
```







## Cache









## **Spring 中的事件处理**



## actuator





## 其他



**SpringBootMavenPlugin**

提供了打包和运行程序的一些命令



**Spring如何解决跨域问题**

但是使用此方法配置之后再使用自定义拦截器时跨域相关配置就会失效。
原因是请求经过的先后顺序问题，当请求到来时会先进入拦截器中，而不是进入Mapping映射中，所以返回的头信息中并没有配置的跨域信息。浏览器就会报跨域异常。

正确的解决跨域问题的方法时使用CorsFilter过滤器。

@CrossOrigin注解

nginx反向代理接口跨域





**CSRF攻击**



**WebSockets**



**无状态服务**







































# **SpringBootSecurity**



**如何保护SpringBoot程序**

使用https

使用snyk检查依赖关系

启用CSRF



**什么是CSRF攻击**

欺骗用户去访问一个认证过(身份未过期)的网站中的一些接口，即盗用用户的身份



**什么是跨域**

浏览器从一个域名的网页去请求另一个域名的资源时，域名、端口、协议任一不同，都是跨域

浏览器的同源策略造成的

域名，协议，端口



**后端如何解决跨域**

1. CorsFilter
2. WebMvcConfigurerAdapter



**Spring Cache常用的缓存注解**

@CacheAble

@CachePut

@CacheEvit



**排除程序包**

basepackage

@SpringBootApplication(exclude=Xxx.class)，注意是全限定类名



**如何配置多数据源**



**多数据源事务如何管理**



**readonly事务管理**



**如何禁用一个自动装配类**



**如何在SpringBoot启动前运行一段指定的代码**

通过ApplicationRunner或者CommandRunner接口



**SpringBoot支持哪些日志框架，默认的日志框架是哪个**

默认使用logback作为日志框架



**如何配置log4j**



**异步方法的调用**



**SpringBootMavenPlugin的作用**

插件？

run 运行

repackage 打包

start，stop 管理应用程序的声明周期





**FreeMarker**



**什么是webSocket**



**SpringBoot优化**



**SpringBoot如何实现共享session**

通常一个项目被分成了多个微服务，各自部署在不同的服务器上，在物理空间上隔离开了，但是我们经常需要在多个微服务间共享session。常见的方案是spring session+redis来实现。

将微服务的session同一保存在redis上，各个微服务读写session都取操作redis。

spring session基于spring中的代理过滤器实现，对于开发人员来说是透明的



**SpringBoot打的jar包和普通的jar包和war包有什么区别**

war是一个可以直接运行的web模块，将war文件包放置它的\webapps\目录下，启动Tomcat,这个包可以自动进行解压，也就是你的web目录，相当于发布了

jar只包含class文件main_class之后是可以是可以用java命令运行的

spring打成的jar包不可以作为普通jar包引入其他项目，无法直接引用。



**如何实现定时任务**

1. 基于注解的，单线程的，多个定时任务，任务的执行时机受上一个任务执行时间的影响

添加@EnableScheduling开启定时任务

方法上添加@Scheduled(cron = "0/5 * * * * ?")指定执行时间

cron中的,-/*?

2. 基于SchedulingConfigurer接口，从数据库中读取指定时间来动态执行定时任务，创建runnable和trigger



# Cloud



## session共享

restful与rpc的对比









# Spring Actuator



# **SpringBatch**



# Spring



**Springboot与Spring，SpringMVC的关系**

Springboot简化了Spring的配置

Springboot内嵌Tomcat

SpringMVC提供了一个web开发的框架



**Spring Boot 的主要优点** 

* 简化了Spring的配置
* 内嵌Tomcat，可以打成jar包独立运行



**Spring Boot ⽀持哪些内嵌 Servlet 容器** 

* tomcat
* jetty



**如何在 Spring Boot 应⽤程序中使⽤ Jetty ⽽不是 Tomcat** 

1. 排除掉tomcat的依赖
2. 添加jetty的依赖



**Spring怎么解决循环依赖的**

Spring有两种依赖注入方式：构造器注入和属性注入

对于构造器注入的，Spring处理不了

对于属性注入的通过三级缓存来解决(单例模式下)

完成了初始化的Bean的映射

完成了实例化，未完成初始化的Bean的映射

单例对象工厂的映射，Bean实例化后加入





## Bean



**Bean的注入方式@Autowire或@Resource的区别**

 @Autowired是Spring的注解，注解是按类型装配依赖对象，默认情况下它要求依赖对象必须存在，如果允许null值，可以设置它required属性为false 

@Resource是Java的注解，默认按照名字进行注入。有两个属性name和type，表示使用名字或类型进行注入



**Spring 中 beanFactory 和 ApplicationContext 的联系和区别**  

这两个接口都可作为容器ApplicationContext 继承了beanFactory ，此外还继承了其他接口(国际化等)，延迟加载与一次加载

BeanFactory：

是Spring里面最低层的接口，提供了最简单的容器的功能，只提供了实例化对象和拿对象的功能；

ApplicationContext：

应用上下文，继承BeanFactory接口，它是Spring的一各更高级的容器，提供了更多的有用的功能

总体区别如下：

1） 使用 ApplicationContext，配置  bean 默认配置是 singleton，无论是否使用，都会被实例化。优点是预先加载，缺点是浪费内存；

2） 使用 BeanFactory 实例化对象时，配置的 bean 等到使用的时候才会被实例化。优点是节约内存，缺点是速度比较慢，多用于移动设备的开发；

3） 没有特殊要求的情况下，应该使用 ApplicationContext 完成，ApplicationContext 可以实现

BeanFactory 所有可实现的功能，还具备其他更多的功能。



**bean 的作用域**

| 作用域         | 描述                                                    |
| -------------- | ------------------------------------------------------- |
| singleton      | bean 默认都是单例的                                     |
| prototype      | IOC容器可以创建多个Bean实例，每次返回的都是一个新的实例 |
| request        | 每个请求创建一个                                        |
| session        | 每个会话创建一个                                        |
| global-session | 所有的Session共享一个Bean实例，Spring5已经没有了        |



**Spring bean 的生命周期**

1. 如果Bean实现了BeanPostProcessor接口，实例化之前Spring就将调用他们的postProcessBeforeInitialization()方法。
2. 如果Bean 实现了InitializingBean接口，初始化时Spring将调用他们的afterPropertiesSet()方法。类似的，如果bean使用init-method声明了初始化方法，该方法也会被调用
3. 如果Bean 实现了BeanPostProcessor接口，Spring就将调用他们的postProcessAfterInitialization()方法。
4. bean实现了DisposableBean接口，Spring将调用它的destory()接口 



**Spring中的Bean为什么用单例**

* 减少实例的创建提高性能
* 减少GC
* 快速获得



**SpringBean是线程安全的吗**

单例的，但不是线程安全的



**BeanFactory 和FactoryBean的区别**



**@Component 和 @Bean 的区别是什么**？ 

1. 作用范围不同



## IOC



**什么是控制反转**

IoC(Inverse of Control:控制反转)是一种设计思想，就是将原本在程序中手动创建对象的控制权，交由**Spring**框架来管理。将对象之间的相互依赖关系交给 IoC 容器来管理，并由 IoC 容器完成对象的注入。这样可以很大程度上简化应用的开发，把应用从复杂的依赖关系中解放出来。



**什么是IOC容器**

IoC 容器是 Spring 用来实现 IoC 的载体， IoC 容器实际上就是个Map（key，value）,Map 中存放的是各种对象





## AOP



**什么是面向切面编程**

面向对象编程的补充，不再使用继承的方式来增强而是使用切面的方式进行增强



**名词解释**

切面：增强的功能类

连接点：

切入点：

通知：增强的方法



**AOP 有哪些实现方式**

- 预编译：AspectJ
- 运行期动态代理（JDK动态代理、CGLib动态代理）：SpringAOP



**Spring AOP 和 AspectJ AOP 有什么区别？** 

Spring AOP 属于运⾏时增强，⽽ AspectJ 是编译时增强。 Spring AOP 基于代理(Proxying)， ⽽ AspectJ 基于字节码操作(Bytecode Manipulation)。 



**springIOC、AOP、静态代理和动态代理的区别**

静态代理时在编译的时候生成一个代理类，动态代理是在运行的时候生成一个代理对象



**项目中SpringAOP的使用**

日志采集



**有哪几种增强**

* 运行前
* 运行后
* 返回
* 环绕
* 异常

```java
try{
      try{
           doBefore();//对应@Before注解的方法切面逻辑
           method.invoke();
       }finally{
           doAfter();//对应@After注解的方法切面逻辑
       }
       doAfterReturning();//对应@AfterReturning注解的方法切面逻辑
   }catch(Exception e){
        doAfterThrowing();//对应@AfterThrowing注解的方法切面逻辑
   }
```



**拦截器和过滤器底层原理**

过滤器 和 拦截器 均体现了AOP的编程思想，都可以实现诸如日志记录、登录鉴权等功能

实现原理：过滤器是基于Servlet的函数回调的，依赖tomcat容器，拦截器 则是基于Java的反射机制（动态代理）实现的。 

触发时机：Filter是进入Tomcat后servlet前触发，拦截器是进入servlet后，controller前触发

```java
@Order(1)
@WebFilter(filterName="firstFilter", urlPatterns="/*")
public class FirstFilter implements Filter {
 
	@Override
	public void init(FilterConfig filterConfig) throws ServletException {
		
	}
 
	@Override
	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
			throws IOException, ServletException {
		System.out.println("first filter 1");
		chain.doFilter(request, response);
		System.out.println("first filter 2");
	}
 
	@Override
	public void destroy() {
		
	}
}
```







## SpringMVC



**SpringMVC工作流程**

1. 发送请求到**DispatchServlet**，DispatchServlet调用**HandlerMapping**通过url找到对应的处理器及拦截器生成执行链返回给DispatchServlet
2. DispatchServlet调用**HandlerAdapter**，HandlerAdapter进行参数校验，格式转换等后执行Handler，返回ModelAndView
3. DispatchServlet将ModelAndView调用**View Resoler**,
4. DispatchServlet将结果返回用户



## SpringBoot



**SpringBoot的核心注解，主要有哪几个注解组成，Spring Boot 的⾃动配置是如何实现的?** 

@SpringBootApplication，主要由@SpringBootConfiguration，@EnableAutoConfiguration，@ComponentScan三个注解组成

@SpringBootConfiguration：等同于@Configuration,说明它是一个配置类，可以用来注入bean

@ComponentScan：自动扫描本包和子包路径下的 @Component(以及@Service等) 注解进行注册 bean 实例到 context 中

@EnableAutoConfiguration：通过判断类路径下有没有相应配置的jar包来确定是否加载和配置这个功能，读取jar包下的配置文件，将需要的bean注入到spring容器中。



**bootstrap.yml与application.yml的区别**

bootstrap.yml用来加载远程配置文件，优先于application，一般在SpringCloudConfig或者Nacos中用到。

bootstrap主要用于从额外的资源来加载配置信息，



**启动SpringBoot后运行一些代码**

* ApplicationRunner接口
* CommandLineRunner接口



**Spring Boot 常⽤的读取配置⽂件的⽅法有哪些** 



**配置文件的优先级**

config>当前目录，工作目录>classPath

1. 工作目录/config
2. 工作目录
3. classpath/config
4. classpath



**Spring Boot 如何做请求参数校验** 

实体类属性上添加@xxx注解和错误消息

接口中的参数前添加@Valid，参数后进阶BindResult接收错误消息

判断BindResult中有没有错误



**如何使⽤ Spring Boot 实现全局异常处理？**

类名上添加 @ControllerAdvice 

定义方法，方法上添加 @ExceptionHandler(value =Exception.class)，方法的参数接受异常



**如何在服务端使⽤ Cookie 呢？**  

* @CookieValue(value="key")
* 从HttpRequest中读取



**如何读取Header**

@RequestHeader("key")



## 其他



**Spring 框架中都用到了哪些设计模式**

* 工厂模式
* 代理模式
* 单例模式
* 



**怎么防止定时任务重复值行多次**



Oauth2协议授权流程简单介绍

Oauth2中后台token是存在服务JVM内存中，如果服务崩了的话，token失效了怎么处理(可以用Redis去实现持久化)



**Spring线程池**



**怎么进行限流**



**SpringMVC不同⽤户登录的信息怎么保证线程安全的** 

* 不要定义类变量
* bean设为原型模式



**Spring怎么解决循环依赖的，哪些是解决不了的 **





# Cloud







配置管理、服务发现、断路器、智能路由、微代理、控制总线、全局锁、决策竞选、分布式会话和集群状态管理等



注册中心Eureka



**APC理论**



**不同注册中心的对比**





















 









