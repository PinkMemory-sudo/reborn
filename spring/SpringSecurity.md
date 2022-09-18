过滤器



WebSecurityConfigurer



HttpSecurity



WebSecurity



## Security自动配置都做了哪些工作

* 创建了一个`UserDetailsService`，账号名为`user`，密码打印在了控制台
* 每个请求创建了一个Filter：`springSecurityFilterChain` 





# 体系结构



## DelegatingFilterProxy



作为Servlet容器和Spring容器的桥梁，DelegatingFilterProxy可以通过标准的Servlet容器标准注册，但是把工作都委托给实现了Filter的SpringBean。

![1650526848387](E:\merge\spring\img\DelegatingFilterProxy.png)



## FilterChainProxy



## SecurityFilterChain



## Security Filters





# 认证



认证机制：

* 账号密码
* OAuth2.0
* SAML2.0



## 账号密码



读取账号密码



账号密码的存储



# 授权