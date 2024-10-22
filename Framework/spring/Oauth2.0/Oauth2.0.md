[参考](https://www.cnblogs.com/javasl/p/13068613.html)



* Oauth2.0
* 流程



Oauth2.0

服务提供方：

资源拥有者

客户端：

多个站点共用一台认证授权服务器。用户经由其中任何一个站点登录后，可以免登录访问其他所有站点。



作用

第三方登录，使用微信/微博登录不需要输入微信/微博的用户名和密码



角色：

授权服务器：

客户端：

多个资源服务器共享一个授权服务器



访问受保护的资源由标准的Spring Security请求过滤器处理



原来的 Spring Security OAuth2.0 已经废弃了，有关 OAuth2.0 的支持已经集成到了 Spring Security 里面了。



认证流程



使用

## 认证流程





## 授权服务

配置UserDetaiDao,实现通过userName获得User的接口

配置加密方法

配置endpoints：token存放位置(默认内存)，实现时间



## 其他服务

配置授权服务的地址

配置http访问权限，哪些路径需要什么权限































