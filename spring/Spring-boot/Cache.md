[参考](https://www.cnblogs.com/mzq123/p/12629142.html#%E4%B8%80spring%E7%9A%84%E7%BC%93%E5%AD%98%E6%8A%BD%E8%B1%A1)



执行过程

使用步骤

核心组件

# 概念



**两个重要接口**

- Cache：缓存抽象的规范接口，缓存实现有：RedisCache、EhCacheCache、ConcurrentMapCache等
- CacheManager：缓存管理器，管理Cache的生命周期





# 使用





**添加依赖**

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

**开启基于注解的缓存**，在启动类或者配置类上添加@EnableCaching

**配置类**

```java
@Configuration
@Slf4j
@EnableCaching  //开启缓存注解驱动，否则后面使用的缓存都是无效的
public class CacheConfig {

    //自定义配置类配置keyGenerator

    @Bean("myKeyGenerator")
    public KeyGenerator keyGenerator(){
        return new KeyGenerator() {
            @Override
            public Object generate(Object target, Method method, Object... params) {
                return method.getName()+"["+ Arrays.asList(params).toString() +"]";
            }
        };
    }
}
```

需要缓存的地方使用`@Cacheable`注解

```java
@CacheConfig(cacheNames = "menuCache",keyGenerator ="myKeyGenerator" )
public interface IMenuService extends IService<Menu> {
    /**
     * 获取用户菜单信息
     *
     * @param username 用户名
     * @return
     */
    @Cacheable(key = "#username")
    List<Menu> getUserMenus(String username);
}
```



**@CacheConfig**

主要用来抽取公共的配置，把它加到类上，应用与所有方法



**@Cacheable**

一个方法或一个类中的所有方法的执行结果将被缓存。参数作为key，执行结果作为value

SpEL expression或者自定义KeyGenerator可以用来替换默认的KeyGenerator来生成key。

每次会检查方法是否已经使用相同的参数调用过，如果不能找到响应的key才会去调用方法并缓存结果。

属性：

`cacheNames` 

方法执行结果缓存在哪个缓存的名字，可以指定多个缓存(redis,EHCache等多种缓存)。与`value`相同,这样就会根据生成的key，去指定的缓存中取数据。

`key`  

Spring Expression Language (SpEL)，用来计算key的表达式，默认使用所有的方法参数来生成(除非自定义了KeyGenerator)

```
常用SpEL
获得方法 #method获得Method， #methodName
获得参数 #方法名
```

`keyGenerator`

自定义KeyGenerator的名字，与`key`属性互斥

`cacheManager`

自定义CacheManager的名字，从哪个缓存管理器里面获取缓存

`cacheResolver`

自定义cacheResolver的名字从哪个缓存管理器里面获取缓存解析器

`condition`

满足条件的进行缓存，默认总是被缓存

`unless`

满足条件的不进行缓存，默认总是被缓存

`sync`

多个线程用相同的key来取数据时是否同步执行

如果同步执行，不能使用unless，只能指定一个缓存，不能组合其他与缓存相关的操作



**@CachePut**

与@Cacheable的不同在于@CachePut每次都会调用方法更新缓存，可用于修改数据

属性与@Cacheable相似



**@CacheEvict**

清除缓存,用于删除操作

```java
@CacheEvict(value = {"emp"}, beforeInvocation = true,key="#id")
public void deleteEmp(Integer id){
  employeeMapper.deleteEmpById(id);
  //int i = 10/0;
}
```

属性与@Cacheable相似

`allEntries`

是否删除这个缓存中的所有数据，默认只删除这个缓存中对应key的数据，`allEntries`与key互斥



`beforeInvocation`

是否在方法执行前进行删除，默认false，方法执行成功或删除



**@Caching**

用于定义复杂的缓存，可以集成`@Cacheable和 @CachePut`

```java
@Caching(
  cacheable = {
    @Cacheable(/*value={"emp"},*/key = "#lastName")
  },
  put = {
    @CachePut(/*value={"emp"},*/key = "#result.id"),
    @CachePut(/*value={"emp"},*/key = "#result.email")
  }
)
public Employee getEmpByLastName(String lastName){
  return employeeMapper.getEmpByLastName(lastName);
}
```







# 核心接口



SpringCache包含两个顶级接口，Cache（缓存）和CacheManager（缓存管理器），就是用CacheManager去管理一堆Cache。



Cache接口

定义了通用的cache操作(get，put，clear操作)



## CacheManager接口

SpringBoot已经为我们自动配置了EhCache、Collection、Guava、ConcurrentMap等缓存，默认使用ConcurrentMapCacheManager。





注解

