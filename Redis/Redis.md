

# 概述



Redis： Remote dictionary server

键值对数据库，整个数据库通通加载内存中进行操作(减少IO操作)，定期通过异步的方式flush到磁盘上。

Redis支持多个数据库，并且每个数据库的数据是隔离的不能共享，并且基于单机才有，如果是集群就没有数据库的概念



**redis与其他缓存产品相比的优势**

* 支持持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用；
* 数据类型丰富
* 速度快，每秒能进行10万读写操作
* Redis支持数据的备份，即master-slave模式的数据备份
* 原子 – Redis的所有操作都是原子性的，意思就是要么成功执行要么失败完全不执行。单个操作是原子性的。多个操作也支持事务，即原子性，通过MULTI和EXEC指令包起来。
* key 过期等等特性。

 



## 安装



[参考](https://www.cnblogs.com/heqiuyong/p/10463334.html)

1. 下载tar包`https://download.redis.io/releases/`



```
docker pull redis:5.0.7
```



[Redis配置文件](http://download.redis.io/redis-stable/redis.conf)

```bash
docker run -p 6379:6379 --name redis \
-v /Users/chenguanlin/Documents/workspace/redis/conf/redis.conf:/etc/redis/redis.conf \
-v /Users/chenguanlin/Documents/workspace/redis/data:/data -d 7eed8df88d3b \
redis-server /etc/redis/redis.conf --appendonly yes
```

* 注意，提前在本地创建好配置文件





## 配置



**配置文件**redis.conf

* 大小写不敏感
* 1k与1kb有区别
* include 路径，可以包含其他配置文件



**允许远程登录**

```
1.bind 127.0.0.1改为 #bind 127.0.0.1 (注释掉)
2.protected-mode yes 改为 protected-mode no
```





**通用配置**

`daemonize yes`  后台运行

`pidFile filePath`   pidfile保存进程号

`loglevel notice` 设置日志级别

`logfile `默认为空，可以设置日志输出目录

`database 0` redis有16个库，默认使用0

`requirepass `默认没有密码

`maxmemory ` **必须设置**，否则占满内存，导致服务器宕机

`maxmemory-policy` redis淘汰策略

`maxmemory-samples` 设置样本数量，LRU算法和TTL算法都是估算值，可以设置样本数据



**连接**

`bind ip`  只能通过指定的ip连接

`protected-mode yes` 默认yes，表示远程不能访问

`port 6379` 端口号默认6379

`tcp-backlog 511`  backlog是一个连接队列=已连接和正在连接的总和

`timeout 0` 多久没有操作会断开连接，0表示无限制，单位s

`tcp-keepalive 300`  超时释放tcp连接

`limits 10000` 客户端最大连接数，默认10000



**设置密码**

```
config set requirepass=123
```



**授权**

```
auth 123
```



**修改配置时不重启怎么生效**

通过config set命令进行配置修改不用重启。config get *获得所有配置，通过redis.conf或者config set修改配置。



# 常用命令





## Redis客户端



Redis 命令用于在 redis 服务上执行操作。要在 redis 服务上执行命令需要一个 redis 客户端

| 命令                             | 描述                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| redis-cli -h host -p port        | 使用本机+默认端口时可以只写redis-cli                         |
| auth password                    | 有密码时要认证                                               |
| config  set requirepass 密码     | 设置密码，立即生效，修改配置文件中的requirepass需要重启redis |
| config get requirepass           | 查看密码                                                     |
| reids-server -port 6379 配置文件 | 启动redis服务                                                |
| ping                             | 用于检查redis服务是否启动                                    |
| info                             | 查看redis信息和状态                                          |
| dbsize                           | 获得键值对数量                                               |



## Key

| 命令                   | 描述                          |
| ---------------------- | ----------------------------- |
| keys  pattern          | 查找符合条件的key             |
| exists key             | 检查key是否存在，不会模糊查询 |
| type key               | 查看key对那个的value的类型    |
| del  key               | 删除key                       |
| flushdb                | 删除所有key                   |
| dump key               | 序列化key，返回序列化后的值   |
| expire key seconds     | 给指定key设置过期时间         |
| EXPIREAT key timestamp | 设置key什么时候过期           |
| MOVE key db            | 讲当前key移动到另一个数据库   |
| PERSIST key            | 移除过期时间                  |
| PTTL key               | 返回剩余的过期时间， 毫秒     |
| TTL key                | 返回剩余时间， 秒             |
| randomkey              | 随机返回一个key               |
| rename key newName     | 重命名                        |
| setnx key value        | 不存在时才插入                |
| renamenx key newkey    | newkey不存在时将key重命名     |

*练习*：

* 如何给一个可以设置过期时间



补充：keys的pattern支持的类型

- `h?llo` matches `hello`, `hallo` and `hxllo`
- `h*llo` matches `hllo` and `heeeello`
- `h[ae]llo` matches `hello` and `hallo,` but not `hillo`
- `h[^e]llo` matches `hallo`, `hbllo`, ... but not `hello`
- `h[a-b]llo` matches `hallo` and `hbllo`



## 数据类型



Redis支持五种数据类型：String（字符串），hash（哈希），list（列表），set（集合）及zset(sorted set：有序集合)。五种类型的操作非常相似，只是存储方式上有所不同



## String

string 类型是二进制安全的。意思是 redis 的 string 可以包含任何数据。比如jpg图片或者序列化的对象。string 类型的值最大能存储 512MB。



**常用的操作**



***赋值***

赋值，赋值并返回旧值，赋值并设置过期时间，同时添加多个键值对，key不存在时才赋值，存在时追加赋值

| 操作                 | 命令                                   |
| -------------------- | -------------------------------------- |
| 赋一个值             | set key val                            |
| key不存在时才设值    | setnx key val nx表示not exist          |
| 获取原值同时设置新值 | getset key value                       |
| 多重赋值             | mset key val key val                   |
| 赋值并指定过期时间   | setex key seconds value 也可以是msetex |
| 追加赋值             | append key value                       |



***取值***

按照key取值，只取value的一部分，同时取多个值

| 操作     | 命令                           |
| -------- | ------------------------------ |
| 取值     | get key                        |
| 多重取值 | mget key key                   |
| 取一部分 | getrange key start end(-1开始) |



***当String中存的是数字时***

| 操作      | 描述                          |
| --------- | ----------------------------- |
| 自增1     | incr key                      |
| 自减1     | decr key                      |
| 自增n     | **incrby** key  n n可为负     |
| 自减n     | decrby key n n可为负          |
| 自增float | incrbyfloat key 值 值可以为负 |



***字符串的操作***

| 操作 | 描述                                      |
| ---- | ----------------------------------------- |
| 长度 | strlen key                                |
| 追加 | append key 添加内容                       |
| 截取 | getrange key 下标1 下标2 闭区间           |
| 覆盖 | setrange key 下标1 值  没覆盖完保持原来的 |



## Hash

一个key对应键值对集合，适合存对象

常用命令与String命令相比，**前面多了个h**

| 操作                           | 描述                                     |
| ------------------------------ | ---------------------------------------- |
| 赋值一个字段的                 | HSET key field value                     |
| 一次赋值多个字段的，与HSET相同 | HMSET key field1 value1 [field2 value2 ] |
| 获得所有hash中的key            | hkeys pattern                            |
| 获得一个hash中的某个字段值     | HGET key field                           |
| 获得一个hash中的所有字段值     | hgetall key                              |
| 字段不存在时设置               | HSETNX key field value                   |
| 一个hash中某个字段是否存在     | HEXISTS key field                        |
| 一个hash中的一个字段自增       | HINCRBY key field increment              |
| hash中浮点数自增               | HINCRBYFLOAT key field increment         |
| 获得字段个数                   | hlen key                                 |

​	

## List



字符串列表，列表最多可存储 232 - 1 元素，一个双向链表，所以在头部或尾部插入

命令以l开头，存是push，删是pop，查看是index



***赋值***

| 操作                                                        | 命令                                                         |
| ----------------------------------------------------------- | ------------------------------------------------------------ |
| 在key对应的右侧插入数据，如果没有key则会先创建再插入        | rpush key value1 value2...                                   |
| 在key对应的右侧插入数据，如果没有key则什么也不操作          | rpushx key value1, value2...                                 |
| `弹出 key 代表的链表的右侧数据；如果不存在 key，则返回 nil` | `rpop key`                                                   |
| 讲弹出的值插入到另一个                                      | `rpoplpush a b`  弹出a的右侧，添加到b的左侧  **只能这样**    |
| 在一个元素之前或之后插入                                    | `linsert key before|after a b``在元素a之后/之前插入b。注意两端不能插` |
| 修改指定index下标的值                                       | `lset key index value`                                       |



***删除***

删除指定的值或者不是该key的值

| 操作                                          | 命令                                                         |
| --------------------------------------------- | ------------------------------------------------------------ |
| `删除链表 key 中值等于 value 的 count 个元素` | `lrem key count value`  值表示删几个(0表示所有) 负号表示方向 |
| `ltrim key start end`                         | 删除未选中的                                                 |



***查看***

| 操作                                | 描述                   |
| ----------------------------------- | ---------------------- |
| `查看 index 位置的元素`             | `lindex key index`     |
| `查看从 start 到 end (包括) 的元素` | `lrange key start end` |
| `查看 key 中元素个数`               | `llen key`             |



## Set

集合是通过哈希表实现的，所以没有重复的元素，添加，删除，查找的复杂度都是 O(1)



***复制***

| 操作                    | 描述                     |
| ----------------------- | ------------------------ |
| `向集合 key 中添加元素` | sadd key member[…member] |



***删除***

| 操作                        | 描述                     |
| --------------------------- | ------------------------ |
| `删除集合 key 中的多个成员` | srem key member[…member] |
| `随机删除一个并返回`        | spop key                 |
| `删除指定元素`              | srem key value1 value2   |



***查看***

| 操作                                | 描述                                                         |
| ----------------------------------- | ------------------------------------------------------------ |
| `显示集合 key 的元素个数`           | scard key                                                    |
| `查询 member 是不是集合 key 的元素` | sismember key member                                         |
| `显示所用元素`                      | smembers key                                                 |
| `随机显示集合中某几个元素`          | srandmember key count count大于集合长度则返回所有，小于0时时返回绝对值个单数可能会重复 |
| `查看是否包含某元素`                | sismember key value                                          |



***集合操作***

| 操作                                                   | 描述                            |
| ------------------------------------------------------ | ------------------------------- |
| `计算集合 key1 和 key2 的差集`                         | sdiff   key1 key2               |
| `计算集合 key1, key2... 的并集`                        | sunion key1 key2 …              |
| `计算集合 key1, key2... 的交集`                        | sinter key1 key2                |
| `计算集合 key1 和 key2 的差集，并将结果存入 目标集合`  | sdiffstore 目标集合  key1 key2  |
| `计算集合 key1, key2... 的并集，并将结果存入 目标集合` | sunionstore 目标集合  key1 key2 |
| `计算集合 key1, key2... 的交集，并将结果存入 目标集合` | sinterstore 目标几个  key1 key2 |



## ZSet

sorted set，有序的set，**每个元素会关联一个分数，根据分数进行排序**，可以做带权重的消息队列。

由hash表或跳跃表实现



***赋值***

| 操作                                          | 描述                      |
| --------------------------------------------- | ------------------------- |
| `向集合 key 中添加元素，附加元素的分数 score` | zadd key score1 member1…… |

即Zset中的元素都会用一个得分，zset会根据得分进行排序



***读取***

读取操作可以按排序的范围/得分的范围/正序/倒序

| 操作                                                         | 描述                         |
| ------------------------------------------------------------ | ---------------------------- |
| 查询指定空间得元素(默认升序)可选性withscores表示是否包含分数 | zrange key 下标1 下标2       |
| 查询指定空间元素，降序                                       | zrevrange key 下标1  下标2   |
| 查询分数在指定空间的元素，inf表示无穷，默认是闭区间，使用（表示开区间  。默认也是降序 | zrangebyscore key min max    |
| 查询分数在指定空间的元素，降序                               | zrevrangebyscore key max min |
| 查看成员分数                                                 | zscore key member            |
| 查看成员个数                                                 | zcard key                    |
| 查看分数在某一区间上的成员个数                               | zcount key min max           |
| 查看成员的名次                                               | zrank key member             |
| 查看成员的倒序名次                                           | zrevrank key member          |



***删除***

| 操作         | 描述                         |
| ------------ | ---------------------------- |
| 按成员名删除 | zrem key member1 member2     |
| 按分数删除   | zremrangebyscore key min max |
| 按排名删     | zremrangebyrank key min max  |



***分数的自增***

`zincrby key 自增的分数 member名`



## Bitmap



**不是一种数据类型**

本身是字符串，只不过能进行位操作。可以当成是由01组成的数据，数组的下标叫做偏移量。

使用偏移量来作为key，偏移量对应的值作为value，用来节省存储时间。

key为数字，value只有01两种状态



**使用场景**

* 适合key连续的，活跃的

**示例**

某用户id,是否开通了该功能。

用户的id作为偏移量，开通为1，未开通为0



| 命令                      | 描述                       |
| ------------------------- | -------------------------- |
| `setbit key offset value` | 将offset对应的bit设置为key |
| `getbit key offset`       | 获得offset对应bit值        |
| `bitcount key`            | 统计bit为1的数量           |



**bitop**

支持四种操作：AND, OR, XOR,NOT。按位操作后保存到另一个key中

***按位与：***`BITOP AND destkey srckey1 srckey2 srckey3 ... srckeyN`

***按位或：***`BITOP OR destkey srckey1 srckey2 srckey3 ... srckeyN`

***按位异或：***`BITOP XOR destkey srckey1 srckey2 srckey3 ... srckeyN`

***按位取反：***`BITOP NOT destkey srckey`

not命令只有一个输入的key，因为它的作用是将bit反转而不是和其他key参与运算

The result of the operation is always stored at `destkey`

进行复合操作时，长度不一样会使用0填充，返回的key的值的长度=最长的长度。



## HyperLogLog



**基数问题**

求集合中不重复的元素的个数的问题



只花费12k的内存，就能计算出2^64个不同元素的基数，只根据输入的元素来计算基数，而不存储元素本身



| 命令·                           | 描述                                          |
| ------------------------------- | --------------------------------------------- |
| pfadd key  values               | 不重复返回1，重复返回0                        |
| pfcount key[key1,key2...]       | 查看key对应的基数,还可以查看多个key组成的基数 |
| pfmerge destkey srckey1 srckey2 | 合并                                          |



## GEO

3.2中新增堆GEO数据的支持。geographic，就是该元素的二维坐标



| 命令                              | 描述                                           |
| --------------------------------- | ---------------------------------------------- |
| geoadd key x y name[key x y name] | 将name的经纬度添加到key中                      |
| geopos key name                   | 将key中name的经纬度取出来                      |
| geodist key name1 name2 [unit]    | 获得两点的直线距离，单位默认m,此外还有km,mi,ft |
| georadius key x y radius unit     | 获得指定坐标指定范围内的name                   |



## 数据的恢复与备份



**save**

save命令用来创建当前数据库的备份，会在redis的安装目录下生成dump.rdb文件

**bgsave** 也可以创建备份文件，表示在后台创建

**CONFIG GET dir**  用来获得redis的安装目录

只需要讲dump.rdb文件放入redis的安装目录，然后重启redis服务





# 管道

[Mass insertion of data](https://redis.io/topics/mass-insert)

**Pipeline**



Redis是一种基于客户端-服务端模型以及请求/响应协议的TCP服务。n个命令需要连接n次Redis

Redis 管道技术可以在服务端未响应时，客户端可以继续向服务端发送请求，并最终一次性读取所有服务端的响应。



**优势**

用来批量操作？





# 发布/订阅



一个Redis客户端可以订阅(创建)多个频道

```bash
SUBSCRIBE channel [channel ...]
```



一个Redis客户端可以将消息发送到指定的频道

```
PUBLISH channel message
```



**使用场景**

聊天，相互订阅发消息





# 事务



与其他数据库的事务不同，Redis事务中任意命令执行失败，其余的命令依然被执行， Redis 事务的执行并不是原子性的，仅作为批量执行的脚本。事务将事务块中的所有命令进行序列化，防止别的命令插队。



**组队时发生异常**(exec之前)

队列中所有命令都不执行

**运行时异常**

其他命令正常执行

使用：
1. MULTI 开启事务
2. 传入多条命令
3. EXEC触发事务
4. DISCARD，放弃事务块里的事务



## 事务冲突的解决


=======
**乐观锁的实现**：WATCH

执行事务前，WATCH key [key ...]监听指定的key，如果key的值发生改变，事务就会中断

## 秒杀案例

* 一个用户只能秒杀一次
* 秒杀结束与秒杀未开始
* 




# 持久化

* 流程
* 触发时机
* 两种方式同时存在



Redis是基于内存的数据库，同时会定期的将数据持久化，保证重启时数据不丢失。

Redis提供类两种持久化方案：RDB和AOF



## RDB

Redis Database

根据指定的时间间隔，至少有n个key发生变化就fork一个子进程，将内存中的数据集快照写入临时文件，最后临时文件替换掉磁盘中的文件(dump.rdb)。整个过程中，主进程不进行任何IO操作。恢复时是将快照文件直接读取到内存。默认就有rdb的持久化。



***优点***：

​	适合大规模的数据恢复，节省磁盘空间

***缺点***：

​	数据完整性和一致性不高，可能会丢失最后一次的备份。



**使用**

配置文件

```
dbfilename dump.rdb
# 生成到Redis的启动命令中
dir ./
# 无法写入磁盘是，关闭redis的写操作
stop-writes-on-bgsave-error yes
# 压缩存储
rdbcompression yes
# 校验快照，会影响性能
rdbchecksum yes
# 指定时间间隔，至少达到指定次数才会进行持久化
save seconds changes
```

**恢复**

将rdb文件放到dir目录中，文件名为dbfilename



## AOF

Append Only FIle

将每个操作追加到日志中(不记录读操作)，重启后将日志中的命令重新执行一次

**AOF默认不开启**

配置

```
# 开启AOF
appendonly yes
# 备份文件名
appendfilename "appendlonly.aof"
# 备份文件位置
dir ./
```

同步频率配置

```
# 每次
appendfync alwys
# 每秒
appendfsync everysec
# 不主动同步，把同步时机交给操作系统
appendfsync no
```



**流程**

* 客户端的请求命令被append到AOF缓冲区中
* AOF缓冲区根据持久化策略，将操作sync到磁盘
* AOF文件大小超过重写策略或手动重写时，对AOF文件rewrite重写，压缩AOF文件
* 重启时，重新执行AOF记录的写操作



**rewite压缩**

AOF文件时追加的，文件会越来越大。rewite压缩会对文件中的命令进行筛选，比如进行ABA操作时只会保留一个A，只关注最后的结果。重写也是fork一个子进程进行操作，与rdb类似。

**触发条件**：文件大于128M时



**异常恢复**

如果遇到AOF文件损坏，可以通过`redis-check-aof --fix appendonly.aof`进行恢复



***优缺点：***

* 一致性高
* 占用磁盘空间
* 恢复慢



## RDB与AOF的对比

* RDB最后一次持久化的数据可能会丢失，适合对一致性不高的情况下
* 两者都开启时，aof文件的优先级高



## **最佳实践**

官方推荐两者一起使用，对数据一致性不敏感可以使用rdb，不建议单独使用aof，而如果只是错纯缓存，两者可以都不用。





# 主从复制



* 什么是主从复制

* 为什么使用主从复制，优势是什么

* 有什么缺点，要注意什么问题
* 主机挂掉或从机挂掉会有什么后果



数据更新到主节点，主节点根据策略自动同步给从节点，主节点用来写，从节点用来读，提高性能，增加容错性，一个从节点出错时可以从其他节点中读。



**缺点：**

* 复制延迟，主服务器将数据复制给从服务器需要时间



**主从复制原理**

从服务器连接到主服务器后，主服务器会对数据进行持久化生成rdb文件交给从服务器(全量复制)

从服务器在进行添加操作后会将数据同步到从服务器(增量复制)



一个从服务器下可以挂它自己的从服务器，主服务器挂掉后可以通过命令(slaveof no one)让该服务器做住服务器

主服务器只会同步到一个从服务器中，然后由该从服务器同步到其他从服务器



主从模式来保证在主节点失败时仍然可用，一个主节点发生问题时会提升它的从节点(副本)作为新的主节点

旧主节点重启后悔变成新主节点的从节点



设置：

哨兵配置文件：sentinel.conf

启动哨兵命令：redis-sentinel 配置文件

```
port：26379 #哨兵端口号 
daemonize yes  #后台启动
sentinel monitor mymaster 主节点ip 主节点端口 2
```





选举规则：

* 优先级(redis.conf的 xxx-priority)
* 偏移量大的优先
* runid最小的



主从模式不要用树状结构，用链式结构。



## 搭建

1. 修改配置文件

可以将基础的部分和主从复制的部分分开，然后用include合并

```
include 
port 
pidfile 
dbfilename
```



2. 使用不同配置文件启动多个Redis实例

```
redis-server 配置文件
```



3. 登录Redis，配置从机

```
slaveOf ip port
```

slaveof  主节点host 主节点port将当前节点设置为指定节点的从节点，主节点有密码时，要在配置文件中设置masterauth



4. `info replication` 查看redis主从复制信息



测试发现：**只能在主机中进行写操作，从机不能进行写操作**



**怎么设置密码？**



## **主从复制原理**

1. 从服务器**连接到主服务器后**，发送**同步数据**的命令
2. 主服务器将当前数据持久化到rdb中发送给从服务器，从服务器读取到数据库中 
3. 每次主服务器进行写操作后，将数据同步到从服务器中

即连接时从服务器获得全量同步数据，之后主服务器发送给从服务器增量数据

**从服务器出现故障**

Redis启动时默认就是Master，从服务器失败重启后，并不会再次加入主服务器，但是数据还在。它现在是一个独立的redis服务，还是需要手动执行`slaveof host prot`来加入主服务器。在从服务器挂掉的期间，主服务器的修改在从服务器重启时会重新进行复制。

**主服务器出现故障**

主服务器挂掉时，从服务器不会选举。Redis启动时默认就是Master，所以主服务器挂掉后，重启还是主服务器，从服务器不会进行任何改变，继续等住服务器回来



## 薪火相传，反客为主

1. 薪火相传。一个从服务器下还可以再挂从服务器`slaveof host port`,将一个从服务器绑定到另一个从服务器。主服务器只需要同步到一个从服务器，从服务器传递同步数据，减少主服务器的压力。
2. 反客为主。每个节点都是全量的数据，所以主服务器挂掉后，从服务器可以变成主服务器。从服务器执行`slave no one`(不再作为谁的从机)



# 哨兵模式



主服务器挂掉时，需要手动的将从服务器变成主服务器，我们应该添加一个监控，让它自动选一个主服务器。

**什么是哨兵模式**

在主从复制下，主服务器挂掉后，从服务器不会做什么改变，从服务器挂掉后，重启时还需要手动再绑定到主服务器。**为了保证可用性**，在主服务器和从服务器上都加上哨兵来监控，达到自动化控制。

哨兵模式是反客为主的自动版，能够后台监控主机是否故障，故障后根据投票数自动将从服务器变为主服务器，主服务器被设置为从服务器。

哨兵模式已经集成在redis的2.4版本中了，是redis集群的解决方案，sentinel来监视master和它的slave



**原理**

1.  每个Sentinel频率向它所知的Master，Slave以及其他 Sentinel 实例发送一个PING命令。 
2.  如果一个实例（instance）距离最后一次有效回复PING命令的时间超过 own-after-milliseconds 选项所指定的值，则这个实例会被Sentinel标记为主观下线 。
3.  如果一个Master被标记为主观下线，则正在监视这个Master的所有 Sentinel 要确认Master的确进入了主观下线状态。 
4.  当有足够数量的Sentinel（大于等于配置文件指定的值）在指定的时间范围内确认Master的确进入了主观下线状态，则Master会被标记为客观下线。 
5. 旧Master会作为新Master的从机



## 配置

1. 新建配置文件：sentinel.conf

```
sentinel monitor mastername host port number
```

* mastername,给master起个名字
* number，至少有number个从服务器的哨兵同意

2. `redis-sentinel 配置文件` 来启动哨兵

 Master-Slave切换后，master_redis.conf、slave_redis.conf和sentinel.conf的内容都会自动发生改变，即master_redis.conf中会多一行slaveof的配置，sentinel.conf的监控目标会随之调换。 



**设置优先级**

配置文件中`replice-priority`值越小，优先级越高



**sentinel的持久化**





**sentinel集群**





**选举规则**

1. 优先级高的(sentinel配置文件中，slave-priority 100,值越小，优先级越高)
2. 偏移量小的(跟主服务器最相近的)
3. runid(启动后自动生成)小的







# 淘汰策略



**什么时候会触发淘汰策略**

 



# 集群



**为什么要使用集群而不是主从复制**

* 一个服务器存不下所有数据，解决容量不够的问题
* 并发的写操作只能在主服务器上执行
* 集群相当于多个主从复制，每一个主从复制负责一段hash槽



**代理主机？**

有多个Redis服务对应多个业务，客户端不请求redis服务，而是请求代理服务器，代理服务器根据规则去请求具体哪个Redis服务。



**去中心化配置**

任何一个Redis服务器都可以作为集群的入口



**什么是集群**

主从模式是将集群的一份数据往从服务器中复制了多分来实现读写分离。而集群是将一份数据分成多分，存储到多个redis节点，而每一份又可以又多个副本。 一个集群又相当于多个主从复制。

* 将一份数据分成多分

* 将一份数据复制多分



**搭建**

修改配置文件

```
cluster-enabled yes
cluster-config-file xxx
cluster-node-timeout 15000
```

创建六个redis实例

加入集群`redis-cli `





# 主从复制和集群的对比



# 分区



就是将数据分隔到多个Redis实例中。



**缺陷**：





# 性能测试工具



# 应用问题



## 缓存穿透

故意查询一些值不符合规范，大部分是遇到攻击了



**解决方案**

1. 空值也缓存。数据库中查不到的数据，Redis中也进行缓存，但是要设置过期时间，防止占用内存资源
2. 设置可访问的名单。查询时先在bitmap查询一下是否可以访问该值
3. 布隆过滤器。对bitmap进行了优化，但是命中率可能不太准确
4. 对Redis进行监控。Redis命中率急剧降低时，运维人员排查一下，可以进行限制IP等操作



## 缓存击穿



当一个key过期时，正好大量用户对这个key进行访问，导致数据库压力忽然增大，打垮数据库。



**解决方案**

1. 预先设置热门数据。高峰期时提前存入，增大过期时间
2. 实时调整。监控热门数据，实时调整过期时间
3. 使用锁(如Redis的setnx)。缓存失效时，不要立即去数据库中拿，而是先获得锁才能去查数据库，这样至于要查询一次数据库



## 缓存雪崩



key大量过期，造成大量请求去查询数据库。



**解决方案**

将过期时间加上随机值

















