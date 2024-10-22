

为什么？

许多配置，比如数据源等，都是一样的，如果修改的话，全部微服务都要修改，一个一个改太麻烦。可以讲共有的提取出来，在外部配置一次。SpringCould Config提供了集中化的外部配置支持。



![1646536014217](..\imgs\Config.png)

ConfigServer读取外部的配置，其他服务通过ConfigServer去读取配置



# 使用



## 配置中心

新建仓库



读取方式



## 客户端

bootstrap与application的区别

* application是用户级，bootstrap是系统级，bootstrap加载优先级更高，默认不会被本地覆盖





### 动态刷新

避免每次更新配置都要重启微服务



添加监控，调用接口去通知微服务刷新配置