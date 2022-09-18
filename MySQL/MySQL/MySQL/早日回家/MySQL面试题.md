# 参考

https://mp.weixin.qq.com/s/wD24xA5ts0YdyMieh_tbZA



# 基础概念



**三大范式**

1NF，每一列不可分割

2NF，消除部分依赖，每一列完全依赖于主键

3NF，消除传递依赖，每一列只依赖于主键

事实上不一定非要满足，经常会为了性能而妥协数据库的设计



**mysql有关权限的表都有哪几个**

MySQL服务器通过权限表来控制用户对数据库的访问，权限表存放在mysql数据库里，由mysql_install_db脚本初始化

这些权限表分别user，db，table_priv，columns_priv和host。

user权限表：记录允许连接到服务器的用户帐号信息，里面的权限是全局级的。

db权限表：记录各个帐号在各个数据库上的操作权限。

table_priv权限表：记录数据表级的操作权限。

columns_priv权限表：记录数据列级的操作权限。

host权限表：配合db权限表对给定主机上数据库级操作权限作更细致的控制。这个权限表不受GRANT和REVOKE语句的影响



# SQL



**varchar(50)中50的含义**

最多存放50字符



**int(20)中20的含义**

类型为int，显示的最大长度是20



**ENUM**

ENUM在存储时实际存储的时整数



**Blog和Text的区别**

BLOB保存二进制数据,TEXT保存字符数据



**记录货币用什么类型好**

常用Decimal和Numric类型表示，这两种类型被MySQL实现为同样的类型。DECIMAL(9,2)9表示整数和小数的一共可以有几位，2表示小数有多少位



**要存储身份证号，要使用什么类型**

像大小固定的字段，可以使用char而不是varchar，这样可以节省空间，提高检索效率。密码散列,用户身份证号等固定长度的字符串应该使用char而不是varchar来存储, 这样可以节省空间且提高检索效率。



**DATETIME与TIMESTAMP的区别**

2. DATETIME占8字节范围是1001-9999年，TIMESTAMP占4字节时间范围是1970-2038
2. DATETIME的存储与时区无关，TIMESTAMP存储时会转换成UTC进行存储



**怎么获得当前日期**

NOW()和CURRENT_DATE().CURRENT_DATE()仅显示年月日



**MySQL的创建时间和修改时间怎么自动更新**

方案1：创建表的时候指定

```mysql
...
`create_time` datetime DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
`update_time` datetime DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间'
...
```

方案2：修改字段

```mysql
ALTER TABLE `course`
MODIFY COLUMN `create_time` datetime NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间' ;
ALTER TABLE `course`
ADD COLUMN `update_time` datetime NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP COMMENT '更新时间' ;
```



**数据库是否支持emoji表情存储**

更改字符集 utf8->utf8mb4



**MySQL自增ID用完怎么办**

ID就不自增了，然后就主键冲突了。

id范围够大了，分库分表也不使用自增的Id。



**一个6亿数据的表A和3亿数据的表B，怎么快速查询处第50000到50200的数据**

id连续时

id不连续时，先查出A表的50000到50200再进行join



**TRUNCATE与DELETE的区别**

* TRUNCATE用来清空整张表，效率高
* 自增约束，TRUNCATE从1开始，DELETE继续往后
* TRUNCATE不支持回滚
* TRUNCATE后没有返回值，DELETE后返回受影响的函数



**drop，delete与truncate的区别**

从执行速度上来说 drop > truncate >> DELETE

truncate删除操作不会记录日志，所以不会恢复

drop是删除整个表，包括表结构



**内连接，外链接什么意思**

内连接：只连接匹配的行

外连接分为左外连接，右外连接和全连接



**count(*)、count(1)、count(column)的区别**

执行结果：count(*)和count(1)没有区别，都不会过滤空值，count(column)不包含空值

执行效率：MyISAM 引擎会把一个表的总行数记录了下来，所以在执行 count(*) 的时候会直接返回数量，执行效率很高，5.5后MySQL的默认存储引擎是InnoDB，因为增加了版本控制(MVCC)的原因，同时有多个事务访问数据并且有更新操作的时候，每个事务需要维护自己的可见性，那么每个事务查询到的行数也是不同的，所以不能缓存具体的行数，他每次都需要 count 一下所有的行数。`InnoDB handles SELECT COUNT(*) and SELECT COUNT(1) operations in the same way There is no performance difference`



[一条SQL语句在MySQL中如何执行的](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247485097&idx=1&sn=84c89da477b1338bdf3e9fcd65514ac1&chksm=cea24962f9d5c074d8d3ff1ab04ee8f0d6486e3d015cfd783503685986485c11738ccb542ba7&token=79317275&lang=zh_CN%23rd)

 **连接器：** 身份认证和权限相关(登录 MySQL 的时候)。

**查询缓存:** 执行查询语句的时候，会先查询缓存（MySQL 8.0 版本后移除，因为这个功能不太实用）。

**分析器:** 没有命中缓存的话，SQL 语句就会经过分析器，分析器说白了就是要先看你的 SQL 语句要干嘛，再检查你的 SQL 语句语法是否正确。

**优化器：** 按照 MySQL 认为最优的方案去执行。

**执行器:** 执行语句，然后从存储引擎返回数据。 



**SQL语句的执行顺序**

FROM

JOIN

ON

WHERE

GROUPBY

AVG,SUM...

Having

Select

Distinct

OrderBy

Limit



**in和exists的区别**

in在查询的时候，首先查询子查询的表，然后将内表和外表做一个笛卡尔积，然后按照条件进行筛选。所以相对内表比较小的时候，in的速度较快。

exist指定一个子查询，检测行的存在。遍历循环外表，然后看外表中的记录有没有和内表的数据一样的。匹配上就将结果放入结果集中



**JDBC使用步骤**

1. 加载驱动
2. 创建连接
3. 获得statement/pre...对象
4. 执行SQL
5. 获得结果集



**什么是SQL预编译**

 很多情况下，一条SQL语句可能会反复执行，或者每次执行的时候只有个别的值不同。指的是数据库驱动在发送 sql 语句和参数给 DBMS 之前对 sql 语句进行编译，这样 DBMS 执行 sql 时，就不需要重新编译。 



 **PreparedStatement为什么能在一定程度上防止SQL注入？** 

 在使用参数化查询的情况下，数据库不会将参数的内容视为SQL执行的一部分，而是作为一个字段的属性值来处理，这样就算参数中包含破环性语句（or ‘1=1’）也不会被执行。



**UNION与UNION ALL的区别**

UNION对两个结果集并集，不包含重复行，同时进行默认规则的排序

UNION ALL对两个结果集进行并集操作，包含重复行，不进行排序。





**超键，候选键是什么**

超键：能唯一标识一条记录的

候选键：最小的超键，没有冗余的元素



**SQL有几种约束**

1. NOT NULL
2. UNIQUE
3. PRIMARY KEY
4. FOREIGN KEY 防止破坏表之间关系和非法插入
5. CHECK



**字段为什么要定义为NOT NULL**

null值会占用更多的资源并且会使索引失效



[MySQL 如何查找删除重复行](https://mp.weixin.qq.com/s/uedKOyRRTBH5WSHM6XUICA)

1. 找出哪些行是重复的，重复行中保留哪一行
2. 建立唯一索引



# 索引



**B树和B+树的区别**

而B+树的非叶子节点只存储key不存储data，这样每个节点中能存储更多key，降低树的高度，减少IO

B+树的叶子节点通过指针相连，容易进行顺序访问



**为什么是B+树**

[为什么 MySQL 使用 B+ 树](https://mp.weixin.qq.com/s/ZEtEZKNJORNz8JoAVq4ziA)

单一节点可以存储更多节点，减少IO次数

所有查询都需要查询到叶子节点，查询性能稳定

所有叶子节点相连，便于范围查询



**索引的分类**

* 普通索引
* 主键索引
* 唯一索引
* 组合索引
* 全文索引(MyISAM支持，只能作用在char，varchar和text。)



**聚簇索引和非聚簇索引的区别**

数据即索引，数据的存储和索引对应，找到索引时就能直接取出数据，所以局促索引只能有一个

非聚簇索引叶子节点存放聚簇索引中的key，需要根据key再从聚簇索引中查。



**索引覆盖**

就是select的字段都在索引中，就不需要进行回表操作了(比如只查询id)。



**索引下推**

对索引中包含的字段先做判断，过滤掉不符合条件的记录，减少回表字数。

不使用索引下推索引时，根据最左匹配原则找出key然后回表查询返回给server，server再判断是否满足where

使用索引下推时，在查询索引时，先根据最左匹配原则，然后会判断where中其他字段是否可以根据该索引再过滤。减少返回的数量，降低回表查询次数。



**什么是回表查询**



**什么样的字段适合建立索引**

* 经常作为查询条件以及排序和分组条件，联结条件的
* 值差异性大的
* 索引的字段的值很长时，最好使用字段值的前缀来进行索引(创建索引时，在索引的字段后加括号指定长度)， MySQL中无法使用前缀索引进行ORDER BY和GROUP BY 
* 不能创建过多的索引，删除不要的索引，尽量扩展索引而不是新建索引



**怎么删除索引**

`删除索引：DROP INDEX 索引名;`



**怎么查看查询中用到的索引**

`explain`可以查看可用的索引和实际使用的索引



**什么情况索引会失效**

* like以%开头
* or语句前后没有同时使用索引
* 在索引列上使用 IS NULL 或 IS NOT NULL操作。索引是不索引空值的，所以这样的操作不能使用索引
* 在索引字段上使用not，<>，!=。会扫描全表
* 对索引字段进行计算操作、字段上使用函数
* 字符串不加引号，如varchar不加单引号的话可能会自动转换为int型
* 没有使用组合索引的第一列索引
* 当全表扫描速度比索引速度快时，mysql会使用全表扫描，此时索引失效



**创建索引的原则**

* 唯一索引
* 经常作为查询，排序，分组等条件的字段
* 限制索引的数量，每次更新时，索引会重建
* 索引字段的值太长的话，可以使用前缀索引
* 删除不再使用或很少使用的索引
* 尽量选择区分度高的列作为索引
* 尽量扩展索引，不要新建索引



**如何添加索引**

1. 创建表的时候

```mysql
create table ......

primarykey(id)
```

2. ALTER TABLE `table_name` ADD PRIMARY KEY ( `column` )



# 事务



**事务的四大特性(ACID)**

原子性：事务是最小的执行单位，不允许分割。事务的原子性确保动作要么全部完成，要么完全不起作用；

一致性：执行事务前后，数据保持一致，多个事务对同一个数据读取的结果是相同的；

隔离性：并发访问数据库时，一个用户的事务不被其他事务所干扰，各并发事务之间数据库是独立的；

持久性：一个事务被提交之后。它对数据库中数据的改变是持久的， 即使数据库发生故障也不应该对其有任何影响。



**ACID实现的原理**

原子性：使用undo log实现。如果出错或用户执行了rollback，系统通过undo log返回开始的状态

持久性：使用redo log。只要redo log持久化了，系统重启后会冲redo log中恢复数据

隔离性：通过锁及MVVM，使事务相互隔离

一致性：通过回滚，恢复，





**事务的隔离级别，MySQL的默认隔离级别是什么**

***读取未提交：***一个事务读取到了另一个事务还没提交的数据，造成赃读，不可重读，和幻读

***读取已提交：***一个事务可以读取已提交的事务，造成不可重读，和幻读

***可重读：***一个事务中多次读取到的结果都是一样的，造成、幻读

***串行读：***完全符合ACID，事务一个一个执行



**什么是赃读幻读不可重读**

* 赃读，读取到其他事务中还没提交的数据，其他数据回滚时读到的数据就是脏数据
* 不可重读，一个事务多次读取数据，期间另一个事务修改了数据，导致读取的数据前后不一样
* 幻读，一个事务读取多行，另一个事务期间插入了几行，导致前后读取到的行数不一致





**InnoDB是怎么解决幻读的**

MySQL InnoDB 存储引擎的默认支持的隔离级别是 **REPEATABLE-READ**(可重读)

InnoDB虽然采用的是可重读的隔离级别，下使用的是Next-Key Lock 锁算法，因此可以避免幻读的产生。

InnoDB 存储引擎在分布式事务 的情况下一般会用到 **SERIALIZABLE(**可串行化**)** 隔离级别。



**事务回滚机制概述**



## redolog



事务写数据时并不会写一条就刷到磁盘中一条，而是记录到redolog中它的变化，宕机是可以从redolog中恢复。



**redolog包含两部分：**

*redo log buffer*

保存在内存中，是易失的。服务在启动时就会申请一块空间作为redolog的缓冲区(默认16M)

*redo log file*

保存在硬盘中，是持久的。



**redo的整体流程**

1. 将磁盘中的数据读取到内存，在内存中对数据进行修改

2. 生成一条redo日志并写入redo log buffer，记录的是数据被修改后的值
3. 当事务commit时，将redo log buffer中的内容刷新到 redo log file，对 redo log file采用追加 写的方式
4. 定期将内存中修改的数据刷新到磁盘中



**redo log刷盘策略**

即buffer什么时候刷如file：定时/每次提交/交给操作系统

redo log buffer刷盘到redo log file的过程并不是真正的刷到磁盘中去，只是刷入到文件系统缓存 (page cache)中去。真正的写入会交给系 统自己来决定。那么对于InnoDB来说就存在一个问题，如果交给系统来同 步，同样如果系统宕机，那么数据也丢失了。针对这种情况，InnoDB给出 innodb_flush_log_at_trx_commit 参数，该参数控制 commit提交事务 时，如何将 redo log buffer 中的日志刷新到 redo log file 中。它支持三种策略：

* 设置为0 :表示每次事务提交时不进行刷盘操作。(系统默认masterthread每隔1s进行一次重做日 志的同步
* 设置为1:表示每次事务提交时都将进行同步，刷盘操作(默认)
* 设置为2:表示每次事务提交时都只把 redo log buffer 内容写入 page cache，不进行同步。由os自
  己决定什么时候同步到磁盘文件。



**为什么不直接刷到页中**

* 频繁进行IO操作
* 页可能并不是连续的



**Checkpoint机制**

* 当数据库发生宕机时，数据库不需要重做所有的日志，因为Checkpoint之前的页都已经刷新回磁盘。数据库只需对Checkpoint后的重做日志进行恢复，这样就大大缩短了恢复的时间。
* 循环利用redolog，当writepos追上checkpiont时，表示redolog满了，checkpoint需要前进一下
* 缓冲区大小优先



## undo log



undo log是事务原子性的保证。在事务中增删改之前实际要先写入一个 undo log ，记录更改之前的数据。

undo log的产生也伴随着redolog，因为undolog也需要持久化



**作用**：

* 回滚，undolog记录的是逻辑上的修改，所以数据可以恢复，但是不一定还按照之前的数据页进行存储
* MVCC，当前行被其他事务使用，可以去读取undo日志中保存的数据，以非锁的方式读取



事务执行完时undolog不会立马清理，应为可能其他事务正在读取(MVCC)



回滚段？

当数据被修改时，原始的数 据会被复制到回滚段



当事务提交时，InnoDB存储引擎会做以下两件事情



InnoDB事务与日志实现的原理



有多少种日志







# 存储引擎



**存储引擎的作用**

索引，MVCC



**MyISAM和InnoDB的区别**

|        | 事务                     | 外键                     | 行级锁                   | MVCC                     | 全文索引                 |
| ------ | ------------------------ | ------------------------ | ------------------------ | ------------------------ | ------------------------ |
| MyISAM | :heavy_multiplication_x: | :heavy_multiplication_x: | :heavy_multiplication_x: | :heavy_multiplication_x: | :heavy_check_mark:       |
| InnoDB | :heavy_check_mark:       | :heavy_check_mark:       | :heavy_check_mark:       | :heavy_check_mark:       | :heavy_multiplication_x: |

* 5.5版本后默认的存储引擎为InnoDB，之前是MyISAM
* 事务：MyISAM不支持事务，最大的缺陷就是崩溃 后无法安全恢复
* 锁： MyISAM 只有表级锁(table-level locking)，而InnoDB 支持行级锁(row- level locking)和表级锁,默认为行级锁。
* 外键：MyISAM不支持外键
* MVCC：仅InnoBD支持，应对高并发事务, MVCC比单纯的加锁更高效;
* 清空整个表时，InnoDB是一行一行的删除，效率非常慢。MyISAM则会重建表

MyISAM强调性能，但是也不一定比InnoDB快，尤其是使用到聚簇索引的时候



**使用场景**

* MyISAM适合查询以及插入为主的应用，
* InnoDB适合频繁修改以及涉及到安全性较高的应用

* InnoDB：需要事务和外键约束时，可以冲灾难中恢复



# 备份与恢复



# 主从复制



**MySQL主从复制解决了哪些问题**

* 读写分离提高数据库并发能力
* 高可用：主数据库出现问题，可以切换到从数据库



**MySQL主备同步的基本原理**



如果要提高并发，首先应该考虑的是优化SQL和索引，其次是缓存，最后再考虑对数据库进行分库分表。



**主从复制原理**

Master将操作写入binlog，Slave拉取Master的binlog拷贝到relaylog，Slave读取relaylog，同步数据

Slave 会从 Master 读取 binlog ，通过Master的一个和Slave的两个线程来进行数据同步。

由三个进程基于binlog完成：

**Master线程**

将binlog发送给从库(并给binlog加锁)

**从库IO线程**

拉取Master的binlig，写入本地的relaylog

**从库SQL线程**

读取relaylog，同步数据



**读写分离常见方案**



**主从延迟问题**

进行主从同步的内容是二进制日志，它是一个文件，在进行 网络传输 的过程中就一定会
 (比如 500ms)，这样就可能造成用户在从库上读取的数据不是最新的数据，也就是主从同步中的 不一致性 问题



**主从延迟怎么解决**

 降低多线程大事务并发的概率，优化业务逻辑 

 是主库和从库服务器的距离尽量要短，提升端口带宽，减少binlog传输 的网络延时 

 实时性要求的业务读强制走主库，从库只做灾备，备份



**一致性校验**

checksum，MySQLLdiff，pt-table-checksum



**使用Springboor+MyBatis操作主从复制**

想要读写分离就需要配置多个数据源，在写操作的时候选择master，在读的时候选择slave



## 一主一从的搭建



**配置master**

1. 修改MySQLServer的UUID

```shell
vim /var/lib/mysql/auto.cnf
systemctl restart mysqld
```

2. 配置文件

```
#[必须]主服务器唯一ID 
server-id=1
#[必须]启用二进制日志,指名路径。比如:自己本地的路径/log/mysqlbin 
log-bin=atguigu-bin
```

2. 创建用于数据同步的账户（目的，让从服务器来复制数据）

**配置slave**

1. 修改配置

```
 #[必须]从服务器唯一ID 
 server-id=2
```

2. 进入MySQL，执行

```
CHANGE MASTER TO
MASTER_HOST='主机的IP地址', MASTER_USER='主机用户名', MASTER_PASSWORD='主机用户名的密码', MASTER_LOG_FILE='mysql-bin.具体数字', MASTER_LOG_POS=具体值;
```

3. SLAVE同步

```bash
#启动slave同步 
START SLAVE;
```



## 多主多从的搭建

需要使用mycat



# 分库分表



**MySQL分库分表的目的是？**

数据量太大导致数据村不下或者性能下降，将原来独立的数据库拆分成若干数据库组成，将数据大表拆分成若干数据表组成， 使得单一数据库、单一数据表的数据量变小，从而达到提升数据库性能的目的。



# 存储过程



**什么是存储过程，怎么调用**

一组为了完成特定功能预编译的SQL 语句集，一次编译后永久有效。如果某次操作需要执行多句 SQL，使用存储过程比单纯 SQL语句执行要快。
调用：

* 可以用一个命令对象来调用存储过程。

* 可以供外部程序调用，比如：java 程序。



**存储过程的优点**

* 存储过程是预编译过的，执行快。
* 存储过程的代码直接存放于数据库中，通过存储过程名直接调用，减少网络通讯。
* 安全性高，执行存储过程需要有一定权限的用户。
* 存储过程可以重复使用，可减少数据库开发人员的工作量。缺点：移植性差





# 触发器



**什么是触发器**

触发器是特殊的存储过程，通过事件触发执行。用来维护数据的完整性和一致性。当出现某个时间(插入修改删除前后)时自动执行这些代码。



**使用场景**

* 数据库中的级联操作
* 监控表中的某个字段发生变化时做出相应的处理



**MySQL中有哪些触发器**

1. Before Insert
2. After Insert
3. Before Update
4. After Update
5. Before Delete
6. After Delete





# 视图



**什么是视图**

通俗的讲，视图就是一条SELECT语句执行后返回的结果集，不存储具体的数据，而是存储视图的定义。所以我们在创建视图的时候，主要的工作就落在创建这条SQL查询语句上。视图是一张虚拟的表，具有和物理表相同的功能，对视图的修改会影响基本表。



**视图的优点**

方便操作，特别是查询操作，减少复杂的SQL语句，增强可读性，视图可以隐藏表表间的复杂关系。

更加安全，数据库授权命令不能限定到特定行和特定列，但是通过合理创建视图，可以把权限限定到行列级别；



**使用场景**

* 授权：客户想要直接查询数据库，不希望用户访问表中某些含敏感信息的列，可以创建视图和用户，将视图授权给用户。

* 表连接：关键信息来源于多个复杂关联表，可以创建视图提取我们需要的信息或者有的时候，由于表中的数据量太大，需要对表进行拆分，这样会导致表的结构发生变化，导致用户的应用程序受到影响，这时我们就可以使用视图来屏蔽实体表间的逻辑关系，去构建应用程序所需要的原始表关系




**视图的分类**

* MERGE：将视图的sql语句和引用视图的sql语句合并在一起，最后一起执行。

* TEMPTABLE： 将视图的结果集存放在临时表中，每次执行时从临时表中操作。

* UNDEFINED： 默认的视图类型，DBMS倾向于选择而不是必定选择MERGE，因为MERGE的效率更高，更重要的是临时表视图不能更新。

所以，这里推荐使用MERGE算法类型视图。



**怎么创建视图**

```mysql
CREATE ALGORITHM = UNDEFINED 
DEFINER = `root`@`localhost` 
SQL SECURITY DEFINER
VIEW `视图名` AS (
    select ...
);

```

ALGORITHM=UNDEFINED：指定视图的处理算法；

DEFINER=`root`@`localhost`：指定视图创建者；

SQL SECURITY DEFINER：指定视图查询数据时的安全验证方式；



**怎么使用视图**

将视图名当成表名

```mysql
select
视图名.属性
from 视图

```





# 内置函数



**获得当前时间**

select current_date()



# 日志



**MySQL的binlog有几种录入格式，有什么区别**





**undolog和redolog的总结**：

事务的运行存储引擎会产生两个log：undolog和redolog，redolog的作用是在事务提交后宕机了，没来得及刷入磁盘，在重启时，可以根据redolog进行恢复，保证了事务的持久性。undolog会记录更新前的数据，用于事务的回滚和MVCC



事务提交后将操作刷如redo log，事务回滚时将执行undo log

redo和undo都是一种恢复操作，redo是物理层的修改记录着物理层上页的修改(页号，偏移量，操作)；undo是逻辑层的修改，会记录一个相反的操作。





## bin log



**binlog录入方式**



redo log与binlog的区别

redolog是存储引擎产生的，binlog是数据库产生的，如果一个事务中对十万行记录进行了插入，这个过程中redolog会不断的记录，binlog只在提交的时候才写入binlog





# 锁



**什么是死锁，怎么解决**





作用：用来实现并发事务的隔离级别

数据库中的数据是共享的，因此在并发事务对数据进行操作时需要加锁。



**悲观锁和乐观锁的区别，怎么实现**



**并发事务的操作**

**读读**

都是读取，不会产生问题，不会加锁。

**写写**

同一时间智能有一个事务进行写，需要加锁。记录和锁结构之间刚开始没有关系，当一个事务需要对记录进行修改时，会先查看有没有锁结构与之关联，没有是就创建一个锁结构与之关联。获得锁成功之后才继续执行事务，获取锁失败会进入等待。

**读写**

一个事务进行读另一个事务进行写时，可能会出现赃读幻读不可重读的情况。

解决方案：读操作进行MVCC，写操作加锁。读已提交和可重读使用到了MVCC。在读已提交中，事务每执行SELECT时会生成一个ReadView，ReadView本省就保证了不会读取未提交的事务。在可重读中，只有在第一次执行SELECT是才会生成一个ReadView，之后的SELECT都会复用这个ReadView





**锁的分类**

按照锁的粒度：

行级锁：

表级锁：读写锁，意向锁

页锁：

按操作类型分：共享锁，排它锁



## 行级锁



**RecordLocks**

单行记录上加锁



**GapLocks**

间隙锁，不包括记录本身



**Next-keyLocks**

record+gap 锁定⼀个范围，包含记录本身。



innodb对于⾏的查询使⽤next-key lock 2. Next-locking keying为了解决Phantom Problem幻读问题 3. 当查询的索引含有唯⼀属性时，将next-key lock降级为record key 4. Gap锁设计的⽬的是为了阻⽌多个事务将记录插⼊到同⼀范围内，⽽这会导致幻读问题的产 ⽣ 5. 有两种⽅式显式关闭gap锁：（除了外键约束和唯⼀性检查外，其余情况仅使⽤record lock） A. 将事务隔离级别设置为RC B. 将参数innodb_locks_unsafe_for_binlog设置为1 



## 表级锁



**s锁，x锁**

在对某个表执行SELECT、INSERT、DELETE、UPDATE语句时，InnoDB不会给表加锁，在对某个表执行一些诸如 ALTER TABLE 、 DROP TABLE 这类的 DDL 语句时



**意向锁**



**自增锁**



**MDL锁**



## 页锁



**InnoDB存储引擎的三种锁的算法**

* Record lock:锁住一个记录
* Gap lock:间隙锁，锁定一个范围，不允许别的事务在间隙中插入数据
* Next-key lock:record+gap 锁定一个范围，包含记录本身(既想锁住某条记录，又想阻止其他事务在该记录前边的进行插入



**MySQL 三种锁的级别**



**MySQL都有哪些锁**



**隔离级别与锁的关系**



**MySQL的乐观锁悲观锁怎么实现**



**表级锁和行级锁对比**

* 表级锁: MySQL中锁定 粒度最大 的一种锁，对当前操作的整张表加锁，实现简单，资源消 耗也比􏰁少，加锁快，不会出现死锁。其锁定粒度最大，触发锁冲突的概率最高，并发度最 低，MyISAM和 InnoDB引擎都支持表级锁。
* 行级锁: MySQL中锁定 粒度最小 的一种锁，只针对当前操作的行进行加锁。 行级锁能大 大减少数据库操作的冲突。其加锁粒度最小，并发度高，但加锁的开销也最大，加锁慢，会 出现死锁。



**乐观锁和悲观锁**

悲观锁如行锁，表锁，读锁，写锁等，都是在做操作之前先上锁。

乐观锁每次去拿数据的时候都认为别人不会修改数据，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据

乐观锁适合在多读的应用类型，这样可以提高吞吐量，如果经常插入，产生冲突的可能性就比较大，上层应用会不断的进行 retry，这样反倒是降低了性能，所以这种情况下用悲观锁就比较合适。



**行锁和表锁的使用场景**



**MySQL的死锁**



# MVCC



**什么是MVCC**

主要用来实现在读写冲突的情况下，避免加锁操作，提高并发能力。即在读取数据遇到锁的情况下，不会等待锁的释放，而是去读快照数据(undolog),一个记录有可能不止一个快照，所以有称为多版本并发控制。

MVCC本质是采用乐观锁思想进行读数据。利用undolog,readview,隐藏字段实现，主要解决读写冲突，提高并发性能。



MVCC 的实现依赖于：隐藏字段、Undo Log、Read View。



一个支持MVCC的数据库，在更新某些数据时，并非使用新数据覆盖旧数据，而是标记旧数据是过时的，同时在其他地方新增一个数据版本。因此，同一份数据有多个版本存储，但只有一个是最新的。



InnoDB的MVCC，通过在每行记录后面保存两个隐藏的列来实现：一个保存了行的创建时间，一个保存行的过期时间（删除时间），当然，这里的时间并不是时间戳，而是系统版本号，每开始一个新的事务，系统版本号就会递增。

由于旧数据并不真正的删除，所以必须对这些数据进行清理，innodb会开启一个后台线程执行清理工作，具体的规则是将删除版本号小于当前系统版本的行删除

select：只查找版本早于（包含等于）当前事务版本的数据行。可以确保事务读取的行，要么是事务开始前就已存在，或者事务自身插入或修改的记录。







**MVCC+Next-Key lock解决不可重读和幻读**





一个事务在执行过程中只有 第一次执行SELECT操作 才会 生成一个ReadView，之后的SELECT操作都 复用 这个ReadView，这样也就避免了不可重复读 和幻读的问题。





为了查询一些正在被另一个事务更新的行，并且可以看到它们被更新之前的值，这样 在做查询的时候就不用等待另一个事务释放锁，提高数据库并发性能。



读数据时会加锁的情况

```mysql
SELECT * FROM student LOCK IN SHARE MODE; # 共享锁 
SELECT * FROM student FOR UPDATE; # 排他锁
INSERT INTO student values ... # 排他锁
DELETE FROM student WHERE ... # 排他锁
UPDATE student SET ... # 排他锁

```







# 优化



## 参考

[别再用 offset 和 limit 分页了，性能太差！](https://mp.weixin.qq.com/s/tFQHqKny6QzoU41PcyrqbA)

[一次 SQL 查询优化原理分析](https://mp.weixin.qq.com/s/lhju1JV2oCqO181s_1STEg)



## **SQL优化**



**offset 和 limit 分页**

*OFFSET 和 LIMIT 有什么问题*

每次收到分页请求时，数据库都需要进行低效的全表扫描(起码要offset+limit行)，当offset越来越大时，就需要进行大量的磁盘IO，而实际上我们只需要limit行，前offset行时不需要的

*解决方案*

方案1：

不再使用offset+limit分页，而是使用自增id加limit分页

如`limit 10000 100`

```mysql
select * from table where id>10000 limit 100
```

弊端：删除了几行记录怎么办

方案2：

join子查询

1. 先只查询主键，找到limit个主键
2. join子查询

这样不用再对前offset个进行回表查询



**优化的步骤**

* 索引
* SQL
* 机器
* 主从复制，读写分离



**慢查询定位**

1. 开启慢查询，设置慢查询时间
2. 分析慢查询日志，找到慢查询的语句
3. 利用explain关键字可以模拟优化器执行SQL查询语句，来分析sql慢查询语句 。
4. type： 显示连接使用了何种类型。从最好到最差的连接类型为const、eq_reg、ref、range、indexhe和ALL  
5. key



**sql优化从哪些方面考虑**

* 是否使用到索引
* 选择合适的字段
* 不存储Null，用0代替
* 避免使用select *
* order by 条件要与where中条件一致，否则order by不会利用索引进行排序



**临时表**

临时存储数据，只在当前连接有效

```
 CREATE TEMPORARY TABLE
```



**大表的优化**

1. 限定表的查找范围
2. 读写分离



小表驱动大表？



优化长难句有什么实战



优化特定类型的查询



优化关联查询



优化子查询



优化LIMIT分页



优化UNION



优化where



优化distinct



数据库结构的优化



MySQL作为发布系统的存储，一天五万的增量，要维持三年，怎么优化



CPU飙升应该怎么处理



慢查询日志



数据库连接池为什么



数据库性能分析show status



* 是否使用了索引

* 是否返回了不需要的字段
* 表数据量太大，考虑分表



**百万级别以上的数据如何删除**

数据的增删改需要IO索引文件，进行上百万次IO所以可以把索引删了，然后进行增删改，最后再加上索引。



**count(*),count(1)与count(列名)的区别**



**MySQL数据库CPU狂飙，怎么处理**

使用show processlist查看session情况，确定是不是又小号资源的SQL执行。



**超大分页，怎么处理**

如果ID是自增且连续的，可以在where后添加id的条件

如果不是连续的，就需要使用覆盖索引



[MySQL高性能优化规范建议](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247485117&idx=1&sn=92361755b7c3de488b415ec4c5f46d73&chksm=cea24976f9d5c060babe50c3747616cce63df5d50947903a262704988143c2eeb4069ae45420&token=79317275&lang=zh_CN%23rd)



[腾讯面试:一条SQL语句执行得很慢的原因有哪些?---不看后悔系列](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247485185&idx=1&sn=66ef08b4ab6af5757792223a83fc0d45&chksm=cea248caf9d5c1dc72ec8a281ec16aa3ec3e8066dbb252e27362438a26c33fbe842b0e0adf47&token=79317275&lang=zh_CN%23rd)



[后端程序员必备:书写高质量SQL的30条建议](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247486461&idx=1&sn=60a22279196d084cc398936fe3b37772&chksm=cea24436f9d5cd20a4fa0e907590f3e700d7378b3f608d7b33bb52cfb96f503b7ccb65a1deed&token=1987003517&lang=zh_CN%23rd)





**大表优化**

[大表优化方案](https://segmentfault.com/a/1190000006158186)

当MySQL单表记录数过大时，数据库的CRUD性能会明显下降，一些常⻅的优化措施如下：

* 限定数据的范围：务必禁止不带任何限制数据范围条件的查询语句。
* 读**/**写分离：主库负责写，从库负责读;
* 垂直分区：把一张列比较多的表拆分为多张表。
* 水平分区：保持数据表结构不变，每一片数据分散到不同的表或者库中，分表仅仅是解决了单一表数据过大的问 题，但由于表的数据还是在同一台机器上，其实对于提升MySQL并发能力没有什么意义，所以 水平拆分最好分库。



## 分库分表



**垂直分区**

优点：可以使得列数据变小，在查询时减少读取的Block数，减少I/O次数

缺点：主键会出现冗余，并会引起Join操作，此外，垂直分区会让事务变得更加复杂。



**水平分区**

表的行数超过200万行时，就会变慢，这时可以把一张的表的数 据拆成多张表来存放。表结构不变，根据某种算法将数据保存到不同的数据库或表中，达到分布式存储。

缺点：分片事务难以解决；跨节点Join 性能􏰁差，逻辑复杂

尽量不要对数据进行分片，因为拆分 会带来逻辑、部署、运维的各种复杂度 ，一般的数据表在优化得当的情况下支撑千万以下的数据 量是没有太大问题的。如果实在要分片，尽量选择客户端分片架构，这样可以减少一次和中间件 的网络I/O。



**垂直分表和水平分表的优缺点**

垂直分表

可以使得列数据变小，在查询时减少读取的Block数，减少I/O次数。此外，垂直分区可以简化表的结构，易于维护。但主键会出现冗余，需要管理冗余列，并会引起Join操作，可以通过在应用 层进行Join来解决。此外，垂直分区会让事务变得更加复杂;

水平分表

支持非常大的数据量存储，应用端改造也少，但 分片事务难以解决 ，跨节点Join 性能􏰁差，逻辑复杂



**数据库分片的两种常⻅方案**

* 客户端代理: 分片逻辑在应用端，封装在**jar**包中，通过修改或者封装**JDBC**层来实现。 当 当网的 **Sharding-JDBC** 、阿里的TDDL是两种比􏰁常用的实现。
* 中间件代理: 在应用和数据中间加了一个代理层。分片逻辑统一维护在中间件服务中。 我 们现在谈的 **Mycat** 、360的Atlas、网易的DDB等等都是这种架构的实现。



数据库中间价mycat



**分库分表之后,id 主键如何处理**

因为要是分成多个表之后，每个表都是从 1 开始累加，这样是不对的，我们需要一个全局唯一的id 来支持。

* UUID:不适合作为主键，因为太⻓了，并且无序不可读，查询效率低。比􏰁适合用于生成 唯一的名字的标示比如文件的名字。
* 数据库自增 id : 两台数据库分别设置不同步⻓，生成不重复ID的策略来实现高可用。这种方 式生成的 id 有序，但是需要独立部署数据库实例，成本高，还会有性能瓶颈。
* 利用 redis 生成 id : 性能比􏰁好，灵活方便，不依赖于数据库。但是，引入了新的组件造成 系统更加复杂，可用性降低，编码更加复杂，增加了系统成本。
* Twitter的snowflake算法 :Github 地址:https://github.com/twitter-archive/snowflake。 美团的Leaf分布式ID生成系统 :Leaf 是美团开源的分布式ID生成器，能保证全局唯一性、 趋势递增、单调递增、信息安全，里面也提到了几种分布式方案的对比，但也需要依赖关系 数据库、Zookeeper等中间件。感觉还不错。美团技术团队的一篇文章:https://tech.meitua n.com/2017/04/21/mt-leaf.html 。



**分库分表的设计**



**分布式主键设计方案**



# 缓存



# 其他



## **数据库中间件**



## 权限



**MySQL有关权限的几张表**

| 表           | 描述                             |
| ------------ | -------------------------------- |
| user         | 允许连接MySQL的账号信息          |
| db           | 记录各个账号在各个数据库少的操作 |
| table_priv   | 记录数据表级的操作权限           |
| columns_priv | 列级的操作权限                   |
| host         | 配合db权限表                     |



**SQL的生命周期**

1. 服务器与数据库建立连接
2. 数据库收到请求的SQL
3. 解析并生成执行计划执行
4. 读取数据到内存，进行逻辑处理
5. 发送结果到客户端
6. 关闭连接，释放资源



**主键使用自增Id还是UUID**

单机的可以使用自增Id，分布式的话优先考虑UUID，一般公司会有自己生成ID的方案。



**自增Id用完了怎么办**



**500台DB，在最快的时间内重启**

批量ssh工具pssh

salt或anslble



**如何监控数据库，慢日志都是怎么查询的**

通过zabbix，lepus



**数据库的六层范式**

终极目标是为了减少数据的冗余，一般情况下只有前三种范式需要满足

1NF:所有字段都是不可分割的

2NF:属性完全依赖于主键，没有部分依赖组件的属性

3NF:解决依赖传递问题





**查询缓冲**

修改配置文件开启缓冲，当查询的表和查询条件都没有变化时可以使用缓存，否则缓存失效。MySQL 8.0 版本后移除，因为这个功能不太实用，缓存虽然能够提升数据库的查询性能，但是缓存同时也带来了额外的开销，每次查询后都要做一 次缓存操作，失效后还要销毁。



# MyBatis



**MyBatis的好处**

* 简化了JDBC代码和取结果集
* SQL配置灵活，SQL与代码分离



**MyBatis与Hibernate有哪些不同**

* MyBatis是半自动ORM框架，需要我们自己编写SQL
* MyBatis移植性差



**#{}和${}的区别是什么**

`#{}` 是预编译处理，`${}`是字符串替换， 使用`#{}`可以有效的防止SQL注入，提高系统安全性 



**参数的传递**

* 实体类：直接使用实体类中的属性名
* Map：使用Map的key获取
* 多个参数：使用#{0},#{1}获取，或者在接口中使用@Param注解，使用注解指定的名字进行获取



**模糊查询like语句该怎么写？**

* 在Java代码中添加sql通配符。

* XML的SQL语句中concat



**Dao接口的工作原理是什么？Dao接口里的方法，参数不同时，方法能重载吗？**

通过接口的全限定名和方法名找到执行的SQL语句，所以参数不同不能重载



**插入时获得主键Id**



**Mybatis如何处理结果集**



**Mybatis 是如何将 sql 执⾏结果封装为⽬标对象并返回的？都 有哪些映射形式？** 

1. 属性和查找的列名相同时自动映射
2. 通过resultMap指定列名和属性名的关系

有了列名与属性名的映射关系后，Mybatis 通过反射创建对象，同时使⽤反射给对象的属性逐⼀ 赋值并返回，那些找不到映射关系的属性，是⽆法完成赋值的。 



**MyBatis如何进行分页**

```
PagrHelper.startPage(number,size);
List<T> list=dao.findAll();
PageInfo<T> pageInfo=new PageInfo(list);
```



**插件的原理**

Mybatis 仅可以编写针对 ParameterHandler 、 ResultSetHandler 、 StatementHandler 、 Executor 这 4 种接⼝的插件， Mybatis 使⽤ JDK 的动态代理，为需要拦截的接⼝⽣成代理对象以实现接⼝⽅法拦截功能，每当 执⾏这 4 种接⼝对象的⽅法时，就会进⼊拦截⽅法，具体就是 InvocationHandler 的 invoke() ⽅ 法，当然，只会拦截那些你指定需要拦截的⽅法。 实现 Mybatis 的 Interceptor 接⼝并复写 intercept() ⽅法，然后在给插件编写注解，指定要拦截哪 ⼀个接⼝的哪些⽅法即可，记住，别忘了在配置⽂件中配置你编写的插件。 



**动态SQL的标签**

```
 <if test="username != null">
           username=#{username}
 </if>
```

where，帮我们在前面添加where，用于查询

```
<where>
    <if test="username != null">
    username=#{username}
    </if>

    <if test="username != null">
    and sex=#{sex}
    </if>
</where>
```

set，帮我们添加set，用于更新

```
<set>
    <if test="username != null and username != ''">
    u.username = #{username},
    </if>
    <if test="sex != null and sex != ''">
    u.sex = #{sex}
    </if>
</set>
```

 trim标记是一个格式化的标记，可以完成set或者是where标记的功能 ，帮我们添加/去掉前缀或者后缀

```
<trim prefix="where" prefixOverrides="and | or">
    <if test="username != null">
    and username=#{username}
    </if>
    <if test="sex != null">
    and sex=#{sex}
    </if>
</trim>
```

有时候可能某个 sql 语句我们用的特别多，为了增加代码的重用性，简化代码，我们需要将这些代码抽取出来，然后使用时直接调用。 

```
<!-- 定义 sql 片段 -->
<sql id="selectUserByUserNameAndSexSQL">
    <if test="username != null and username != ''">
        AND username = #{username}
    </if>
    <if test="sex != null and sex != ''">
        AND sex = #{sex}
    </if>
</sql>

<!-- 引用 sql 片段，如果refid 指定的不在本文件中，那么需要在前面加上 namespace -->
<include refid="selectUserByUserNameAndSexSQL"></include>
```

批量操作foreach

```

```





**分步查询**



**MyBatis的延迟加载**

 Mybatis仅支持association关联对象和collection关联集合对象的延迟加载 



**一级缓存与二级缓存**

一级缓存: 基于 PerpetualCache 的 HashMap 本地缓存，其存储作用域为 Session，当 Session flush 或 close 之后，该 Session 中的所有 Cache 就将清空，默认打开一级缓存。
二级缓存与一级缓存其机制相同，默认也是采用 PerpetualCache，HashMap 存储，不同在于其存储作用域为 Mapper(Namespace)，并且可自定义存储源，如 Ehcache。默认不打开二级缓存，要开启二级缓存，使用二级缓存属性类需要实现Serializable序列化接口(可用来保存对象的状态),可在它的映射文件中配置 ；
对于缓存数据更新机制，当某一个作用域(一级缓存 Session/二级缓存Namespaces)的进行了C/U/D 操作后，默认该作用域下所有 select 中的缓存将被 clear。



**ENUM映射**



