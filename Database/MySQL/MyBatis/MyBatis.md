* ORM框架
* 使用流程
* 配置
* 映射
* 从XML创建SqlSessionFactory
* 指定Mappe.XML位置，加载Mapper的方式
* 动态SQL
* 延迟

* 与SpringData的继承
* 映射关系
* 相关配置
* 数据库连接池
* 动态SQL
* 占位符
* 一对多，多对一
* 别名
* 驼峰转化
* 缓存
* 逆向工程
* 联合主键





* 分组，排序，分页



原始的流程

1. 根据XML(全局配置文件)创建一个SqlSessionFactory
2. 通过SqlSessionFactory来获得一个SqlSession
3. 通过SqlSession执行SQL(SQL写在XML中，并且路径要配置到全局配置文件中)
4. 关闭SqlSession





XML

通过namespace和Id使得XML与接口绑定，XML生成一个代理对象



#与&



* 配置文件
* 映射文件
* 动态SQL
* 缓存
* 逆向工程
* 插件



# 基础概念



**优点：**

* MyBatis简化了JDBC连接代码和结果集映射代码。

* 使SQL与代码分离，进行解耦。
* 可以自定义映射结果



**半自动持久层框架**





# Demo

1. 添加依赖
2. 配置数据原
3. 指定xml和接口的位置
4. 编写接口和映射文件

**添加依赖**

```xml
<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
  <scope>runtime</scope>
</dependency>
<dependency>
  <groupId>org.mybatis.spring.boot</groupId>
  <artifactId>mybatis-spring-boot-starter</artifactId>
  <version>2.2.2</version>
</dependency>
```

**配置数据源**

```properties
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://127.0.0.1:3306/test_mybatis?useUnicode=true&characterEncoding=utf-8&useSSL=true&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=123456
```

**指定映射文件和接口文件位置**

***在配置文件中指定映射文件位置***

```properties
mybatis.mapper-locations=classpath:mapper/*Mapper.xml
```

可以逐个指定或者批量指定：

在接口上逐个添加ibatis的@Mapper注解，或者直接在启动类上使用MapperScan指定哪些包下的接口需要代理

**编写接口和映射文件**

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >

<mapper namespace="com.pk.springboot.mapper.UserMapper">
    
    <select id="count" resultType="int">
        SELECT count(*) FROM user
    </select>
    
</mapper>
```

***注意***：

* namespace要指定接口的全限定名
* id指定接口中的方法名





# 全局配置文件

常见的配置：

* datasource
* 连接池
* 事务管理器
* 别名 
* 驼峰

[MyBatis在SpringBoot中的配置](cnblogs.com/huiy/p/9278394.html)

引入dtd约束，在写全局配置的XML时会有提示

**properties**

使用properties标签引入外部properties文件，比如将数据源的配置信息抽取到另一个配置文件然后再导入进来，就可以引用properties中的变量。



**settings**

用来设置mybatis的运行行为

map。。。

下划线与驼峰转换



**typeAliases**

主要解决Java类需要全限定名(太长)的问题

给**Java类**起一个简单的别名,这样使用全类名时可以用别名替换。可以为某个/批量起别名。别名不区分大小写。还可以在实体类上使用@Aliass来其别名。

已经默认存在的别名：

日期

基本类型及包装类型



**typeHandlers**

类型处理器，Java数据类型与数据库类型的转化

：JSR310



**plugins**

拦截SQl的执行，在执行前后进行增强 (Executor,parameterHandle,ResultSetHandle,statementHandler)



**enveroments**

测试和开发用的环境不同，可以配置多个环境，每个环境必须有dataSource和transactionManager配置



**databaseIdProvider**

MyBatis可以执行不同厂商的SQL。在mapper.xml中可以指定databaseId来指明该SQL语句由哪个厂商执行



**mappers**

指定SQL映射文件(mapper.xml)的位置



这些标签都是有顺序的



## **常用配置**

```properties
# 驼峰转换
# 设置别名
# xml文件的目录
```







# 映射文件

映射文件通过namespace与接口关联，通过标签的id与对应的接口的方法关联，可以看成是方法的具体实现。



## 增删改



**标签属性**

![image-20220221133552581](../../img/MyBatis-增删改标签属性.png)



**insert**

```xml
<insert id="方法名" parameterType="",>
    sql语句
</insert>
```

* 取对象的值可以直接使用#{属性名}来获取
* parameterType可以省略

**自动生成主键**

useGeneratedKeys设置为true，keyProperty指定属性名

```xml
<insert id="addEmployee" useGeneratedKeys="true" keyProperty="id">
    INSERT INTO employee VALUES(null,#{name},#{age},#{dpId})
</insert>
```

**注意**：自增的id会被赋值给实体类中的id属性，而方法返回的int只是受影响的个数



```xml
<delete id="方法名">
    SQL语句
</delete>
```



增删改可以直接返回int，long，boolean。无需在mapper文件中指定，接口中指定就行。



**insert时获得自增的id**

insert标签添加配置

* useGenerateKeys告诉mybatis使用主键自增策略
* ketProperty，指定主键值交给谁

```xml
<insert id="方法名" parameterType="" useGenerateKeys="true" ketProperty="id”>
    sql语句
</insert>
```



## 查

![image-20220221135037886](../../../merge/img/MyBatis-查询标签属性.png)



**模糊查询**

```xml
<select id="findByPrefixName" resultType="Employee">
    SELECT * FROM employee
    WHERE name like #{prefixName}"%"
</select>
```

注意%要加双引号



## 参数传递



1. **接口只有一个参数时**

#{x}x随意写都可以获得这个参数值



2. **接口的方法中有多个参数时**

接口的方法中有多个参数时，MyBatis会把他封装成一个map。封装成map时，key就是param0...paramN

#{}就是从map中取值。所以多个参数时通过param或者索引下标获得(#{param0}或者#{0})。

如果不想使用#{param0}或者#{0}，可以在接口方法的参数前加***@Param***来指定参数的key。



3. **封装成Map或者类**

而如果自己封装成map时(或对象)，就可以使用自己map中的key来获得值。

所以，**需要多个参数时建议封装一下**



**#{name}中用不用指定双引号？**

不用



## **结果集映射**



**不指定返回值类型**

返回的是受影响的行数



### resultType

**映射实体类**

指定resultType。*resultType*用来自动ORM转换，直接指定结果集要转换成的类型

MyBatis已经为许多常见的 Java 类型内建了相应的类型别名。它们都是大小写不敏感的。



**映射List**

与映射实体类相同











**日期类型的处理**





***1. 返回List***

resultType中指定集合的泛型而不是List

***2. 返回Map***

resultType指定为map

***3. 封装成<String,实体类>***

resultType中指定实体类，在接口的方法中添加@MapKey注解指定谁用来做Map的key



### **resultMap**

不能直接指定映射类型的，需要我们指定怎么映射，用来封装更加复杂的结果集映射。resultMap中没有指定的列会尝试自动映射。

与resultType**只能二选一**

1. ***自定义一个resultMap***

```xml
<resultMap id="UserInfo" type="com.pk.springboot.model.UserInfo">
  <id column="uid"  property="id"/>
  <result column="name" property="name"/>
  <result column="age" property="age"/>
  <result column="pid" property="companyId"/>
  <result column="useName" property="name"/>
  <association property="companyEntity" javaType="CompanyEntity">
    <id column="pid"  property="id"/>
    <result column="pname" property="name"/>
  </association>
</resultMap>
```

2. ***使用自定义的resultMap***

```mysql
<select id="" resultMap="MyMap">
</select>
```

* 自定义id为resultMap的名字
* type为要映射成什么类型

子标签：

| 子标签      | 描述                                     |
| ----------- | ---------------------------------------- |
| id          | 标记id属性名和返回的列名映射，可提高效率 |
| result      | 标记属性名和返回的列名映射               |
| association | 如果属性是一个对象，使用association映射  |
| collection  |                                          |

**属性**

id和result来指定字段映射成什么属性	![image-20220221140209372](/Users/chenguanlin/Documents/workspace/merge/img/MyBatis-id和result的属性.png)



**自动映射**

* autoMappingBehavior默认是PARTIAL，开启自动映射的功能。唯一的要求是列名和javaBean属性名一致

* 如果autoMappingBehavior设置为null则会取消自动映射

* 数据库字段命名规范，POJO属性符合驼峰命名法，如A_COLUMNaColumn，我们可以开启自动驼峰命名规
  则映射功能，mapUnderscoreToCamelCase=true。

* ```
  mybatis.configuration.auto-mapping-behavior NONE 取消自动 PARTIAL:部分  FULL:全部
  ```



#### association

**一对一映射**

员工对象Employee，每个员工属于一个部门Department，所以Employee中都一个Department对象，这时候结果集中的字段该如何与Employee实体类映射？

**方法一**：*使用级联属性封装*

```xml
<resultMap type="" id="">
    <result column="字段名" property="Department.属性名"></result>
</resultMap>
```

**方法二**：使用*association指定联合的JavaBean*

```xml
<resultMap type="" id="">
    <association property="哪个属性名时需要联结的" javaType="联结成什么JavaBean">
    <!--根据情况在对嵌套的JavaBean进行映射-->
    </association>
</resultMap>
```

**方法三**：*分步查询*

在关联映射时，不再是一下查出来，而是先查出人，通过association的select标签根据部门Id查部门，将结果返回给对象的属性。

```xml
<resultMap type="" id="">
    <association property="哪个属性名时需要联结的" select="..." coumn="...">
    <!--根据情况在对嵌套的JavaBean进行映射-->
    </association>
</resultMap>
```

* select:表明当前属性是调用select指定的方法查出的结果
* column:指定将哪一列的值传给这个方法

传递多个值

```xml
coumn="{key1=colume1...}"
```



#### collection

**一对多映射**

一个部门中有多个员工，Department有个List\<Employee>

方法一：*使用collection指定List的映射*

```xml
<collection property="实体类中List的属性名" ofType="List的泛型">
	<!--根据情况是狗需要指定List泛型的映射-->
</collection>
```

**方法二：分布查询**



### 总结

通过id或者result标签指定cloumn和property的映射关系，如果属性值是对象的话用association如果属性的值是集合用collections。



**延迟加载：**

resultMap中使用association和collection可以延迟加载，先从单表查询、需要时再从关联表去关联查询，大大提高 数据库性能，因为查询单表要比关联查询多张表速度要快。



# 动态SQL

* 简化动态拼接SQL

**遇见特殊符号需要使用XML的转义字符**，比如&&



## if

有时候需要根据过滤条件去拼接SQL的查询条件，有哪些过滤条件就拼上对应的SQL语句

```xml
<if test="判断表达式(OGNL表达式)"></if>
```



**问题一：** 第一个if不满足，第二个if满足时，前面就会多个and

方案1：最前面加一个1=1，以后的条件就都可以在前面加and了

方案2：使用where标签，将if标签都放在where标签中，如果where中第一个语句多出and会自动去掉。使用where标签的前提是每个and都写在语句的前面。





## choose

分支选择，多选一

![image-20220222103736038](../../../merge/img/MyBatis-动态SQL-choose.png)



## where

可以去掉前面多余的and



## **set**

主要用来封装修改条件，带了哪些属性就修改哪些值

set field=value,field2=value2, 可能最后会多一个,。把更新条件写在set标签中，set会去掉最后的逗号



## trim

trim用来处理拼装完成的SQL语句，去掉/加上一些前缀或后缀，等同于where+set

| 属性             | 描述         |
| ---------------- | ------------ |
| prefix           | 添加指定前缀 |
| prefisOverrides  | 去掉指定前缀 |
| suffix           | 添加指定后缀 |
| suffixOverirides | 去掉指定后缀 |



## foreach

遍历出的元素赋值给指定元素

使用场景：

1. in中

比如需要`select * from table_name where id in (...)`,就需要遍历集合拼接出in语句

2. 批量保存/修改中

```xml

```



| 属性      | 描述                     |
| --------- | ------------------------ |
| item      | 元素别名                 |
| index     | 指定一个元素来代表下标   |
| separator | 每次迭代添加一个separate |
| open      | 以什么开始               |
| close     | 以什么接受               |



## bind

将绑定到一个变量方便以后引用



## sql

抽取可以重用的sql语句



# 缓存机制



**一级缓存**

默认情况下，只有一级缓存（SqlSession级别的缓存， 也称为本地缓存）开启。

每当我们使用MyBatis开启一次和数据库的会话，MyBatis会创建出一个SqlSession对象表示一次数据库会话。一级缓存就是在我们与数据库建立连接创建SqlSession后如果是相同的SQL就不再发给数据库。MyBatis会在表示会话的SqlSession对象中建立一个简单的缓存，将每次查询到的结果结果缓存起来，当下次查询的时候，如果判断先前有个完全一样的查询，会直接从缓存中直接将结果取出，返回给用户，不需要再进行一次数据库查询了。

一级缓存失效情况：

* 与数据库的会话结束
* 一个会话中执行了任何的增删改操作。



**二级缓存**

二级缓存是用来解决一级缓存不能跨会话共享的问题的，是基于namespace级别的缓存，可以被多个SqlSession 共享（只要是同一个接口里面的相同方法，都可以共享。

生命周期和应用同步。如果你的MyBatis使用了二级缓存，并且你的Mapper和select语句也配置使用了二级缓存，那么在执行select查询的时候，MyBatis会先从二级缓存中取输入，其次才是一级缓存，即MyBatis查询数据的顺序是：二级缓存  —> 一级缓存 —> 数据库。





# 插件

MyBatis在四大对象的创建过程中，都会有插件进行介入(Executor,ParameterHandler,ResultSetHandler,StatementHandler)。



## 分页

1. 添加依赖

```xml
<!-- pagehelper -->
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.2.5</version>
</dependency>
```

2. 配置文件中设置

```yaml
# pagehelper   
pagehelper:
    helperDialect: mysql
    reasonable: true
    supportMethodsArguments: true
    params: count=countSql
```

3. 创建DAO(和不分页的代码相同)
4. server层，调用DAO之前先执行PageHelper.startPage(pageNum, pageSize);将分页查询参数传入
5. 调用完之后，使用PageInfo构建查询结果

```java
PageHelper.startPage(pageNum, pageSize);
List<SysUser> sysMenus = sysUserMapper.selectPage();
new PageInfo<SysUser>(sysMenus)
```



## 批量操作

批量操作我们是使用MyBatis提供的BatchExecutor进行的， 他的底层就是通过jdbc攒sql的方式进行的。我们可以让他 攒够一定数量后发给数据库一次

upserter



# 逆向工程

即代码生成器。

配置数据库连接池

事务

什么情况下需要使用resultType

自动映射的级别和两种方式



# Exception



一对多映射时出现**TooManyResultsException**

检查resultMap与select的返回的字段是否能映射上，id标签是一定要写的。



`Invalid bound statement (not found): com.pk.mybatis.mapper.EmployeeMapper.addEmployee`

配置文件中指定的mapper-location是否正确

xml文件中namespace和id是否与接口对应



`Cannot find class`

需要指定实体类目录来设置别名，否则就写全限定名

```properties
mybatis.type-aliases-package=com.pk.mybatis.mybatis.entity
```



` A query was run and no Result Maps were found for the Mapped Statement `

DELETE,INSERT,UPDATE不用指定映射，因为它们返回的是受影响的行数，SELECT必须指定返回值如何映射