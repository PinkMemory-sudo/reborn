# Kafka



**Kafka定义**

分布式的基于发布订阅的消息队列，主要用于大数据的实时处理领域。

最新定义：分布式事件流平台，进行数据的存储，分析计算，类似Spark，Flink

生产者消费者的代码由Java编写，Broker由Scala编写



场景

统计

centipede 更新事件



安装

安装 ZK



Docker 安装

https://www.cnblogs.com/yxlblogs/p/10115672.html



docker run -d --name kafka --publish 9092:9092 --link zookeeper --env KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181 --env KAFKA_ADVERTISED_HOST_NAME=10.83.49.158 --env KAFKA_ADVERTISED_PORT=9092 --volume /etc/localtime:/etc/localtime wurstmeister/kafka:latest





* 处理持续的数据流，把数据开成实时的数据流
* 



Kafka 与 MQ 的关系

Kafka 可以看成是一个MQ，但 MQ 通常用来传递消息，Kafka的流出了可以方便的动态处理派生流和数据集



**发布订阅消息系统**

为什么要发布订阅

解耦：刚开始两个服务直连，后来不满足需求了，需要多个服务，就需要修改多个服务的代码，可以独立出来一个程序专门处理



规模可以随业务的增长而增长



消息：字节数组，所以消息没有特别的格式/含义

key：字节数组，控制消息写入的分区，相同 key 写到相同的分区



**schema**

消息是晦涩难懂的字节数组，可以使用额外的结构来定义消息的内容，



**Topic**

对消息进行分类，一个 Topic 可以分成多个分区，消息以追加的方式写入分区，同一分区才能保证消息的顺序。key 或分区器来实现分区



**消费者**

消费者通过偏移量区分已经读过的分区

每个分区只能被一个消费者订阅

一个消费者失效，可以有同组的其他消费者替代



**分区**

分区复制



Broker

一个独立的 Kafka 服务称为一个 Broker，



Kafka 消息保留策略

时间/大小



多集群(多数据中心)



Kafka默认副本是一个，生产环境一般配置两个



# 安装



ZK

保存集群的原信息和消费者信息

ZK 使用一致性协议，所以每个群组应该包含奇数个节点



验证安装是否成功

```bash
# 创建 topic
kafka-topics.sh --create --zookeeper x.x.x.x:2181 --replication-factor 1 --partitions 1 --topic test
# 查看topic
kafka-topics.sh --zookeeper xxxx:2181 --describe --topic "test"
# 生产者
kafka-console-producer.sh --broker-list xxx:9092 --topic test
# 消费者
kafka-console-consumer.sh --bootstrap-server xxxx:9092 --topic test --from-beginning
```



## Broker 配置

单机可以直接使用默认值，集群需要修改

broker.id

每个 Broker 都需要一个标识符，默认为 0，在集群中要唯一

port

默认监听 9092 端口

zookeeper.connect

指定用于保存 broker 原书的的 Zookeeper 的地址，localhost:2181/chroot_path

log.dirs

指定存放路径，如果指定多个路径，同一分区的日志片段保存在同一路径下

num.recovery.threads.per.data.dir



auto.create.topics.enable

Kafka会自动创建主题

* 当生产者往主题写消息时
* 当消费者开始从主题读消息时
* 当任意一个客户端向主题发送元数据请求时

可以设为 false



主题的默认配置

num.partitions

分区个数,分区个数可以添加不能减少

**如何确定分区个数**

吞吐量是多少，每秒 100KB 还是 1GB，如果需要 1GB 吞吐量，一个消费者每秒处理 50M，那么需要20 个消费者，至少需要 20 个分区

**数据保留策略**

log.retention.ms

默认值 168H(一周)

log.retention.bytes

设置成 1GB 时，如果有 8 个分区，最多就能保存 8GB 的数据

两个保留策略都配置时，有一个满足log就会被请求

log.segment.bytes

消息到达 broker 时，会追加到当前的日志片段上，当达到log.segment.bytes时就会生成新的分片，关闭当前分片，等待过期

log.segment.ms

通过时间控制 log 段



message.max.bytes

用来限制单个消息的大小，默认值为 1M(压缩后的消息)

超过这个大小，不仅不会接收，还会返回错误









# 生产者



**消息发送的原理**



如何创建

如何发送

如何处理返回的错误

配置

Kafka 不局限于 Java，它提供了二进制连接协议，只要想 Kafka 的端口发送适当的字节序列，就可以发送和读取数据



多样的场景对应多样的需求

* 是否允许消息丢失
* 是否允许消息重复
* 有没有延时和吞吐量的要求



**批次**

为提高效率，消息分批次写入 Kafka，同一个批次 Topic 和分区相同



**向 Kafka 发送消息的主要步骤**

1. 生产者创建 ProducerRecoder，包含 Topic，[Key]，[Parttition]，value
2. **序列化**成字节数组
3. 通过**分区器**，生产者知道了将消息发送到哪个分区，然后将消息添加到对应的批次(内存中的**缓冲队列**)，Sender线程会读取缓冲队列，将达到16k或者到时间的批次中的消息发送到同一主题的同一分区
4. Broker 收到消息，成功写入 kafka 后返回一个 RecordMetaData 对象，包含分区和偏移量等信息，如果失败返回错误，生产者收到错误会进行重试，几次都失败后就返回错误信息。



**应答机制**

ack

必须有多少个分区副本收到消息后，生产者才任务消息写入成功

0 表示不关心，不等待服务器的相应

1 集群首领节点收到消息后

all 所有副本都受到后



**发送消息的三种方式**

发送并忘记：只管发送，不关心结果，可能存在消息丢失风险

异步发送：发送消息并指定回调函数，返回时调用该函数

同步发送：发送并调用 get()等待是否成功,失败时 get 会发生异常





无法通过重试解决的错误(如消息太大)不会进行错误重试，会直接发送错误



**Kafka 生产者 3 个必要的属性**

bootstrap.servers

指定 broker 的地址清单，不需要包含所有的 broker，生产者或根据给定的 broker 获得其他 broker，建议至少配两个，防止一个宕机

key.serializer

key 的序列化，虽然不一定使用 key，但是必须设置

value.serializer



**其他参数**

buffer.memory

设置生产者内存缓冲区大小

compression.type

默认发送消息时不会被压缩，该参数可以是 snappy，gzip，lz4

**retries**

服务器返回的错误可能是临时行的，retries 指定了一颗重新发送的次数，如果达到这个次数还不行，生产者就放弃发送返回错误，默认每次发送会建个 100ms，可以通过 retry.backoff.ms改变

**batch.size**

一个批次可以使用的内存大小(字节数)，填满时消息就会发送，不过不一定等待批次填满，

**linger.ms**

等待多长时间后发送消息。默认情况下，只要有可用线程，就会发送消息。

**client.id**

服务器用来视频消息来源

**max.in.flight.requests.per.connection**

生产者在收到服务器响应前可以发送多少个消息，值越高，占用的内存越大，设置 1 可以保证消息按照发送的顺序写入 Kafka，但影响吞吐量

**timeout.ms、request.timeout.ms、metadata.fetch,timeout.ms**

**max.block.ms**

send 方法或者 partitionsFor()获得元数据时生产者阻塞的时间。

**max.request.size**

**receive.buffer和 send.buffer.bytes**

## 分区



**好处**

* 扩大存储能力
* 扩大处理能力



只有在不改变分区数量的情况下，key 与分区的映射才不会改变，如果要使用 key，那么最初就应该吧分区规划好



**自定义分区器**

需要将某些消息分配到单独的分区，重写Partationer接口



# 消费者



**消费方式**

pull or push

kafka采用主动从broker中拉去的方式。

优点：根据消费者自身的情况去拉取消息。

缺点：如果broker中没有消息，一直返回空数据，影响效率。



offset存储在Kafka的一个Topic中，老版本中存储在ZK里。



**消费者组**：groupID相同的消费者





**消费者组的初始化和分区的选择**

* 根据GroupId的hashcode%50(consumer_offsets分区数量)选择coordinator来提交offset，所有消费者向这个coordinator发送消息注册
* coordinator随机选一个leader，把信息传递给leader，leader将消费者分区分配然后返回coordinator

* coordinator再发送给所有消费者它们要消费的分区。
* 消费者与coordinator之间位置心跳，超时未相应会触发再平衡，消费者超时未处理完也会进行再平衡



**Kafka的分区策略**

* range
* RoundRobin
* Sticky
* CooperativeSticky

默认策略时range+CooperativeSticky。

Range的缺点：

* 容易产生数据倾斜

![image-20221121195159190](C:\Users\11942\AppData\Roaming\Typora\typora-user-images\image-20221121195159190.png)

**消费者使用步骤**

![image-20221121195353978](C:\Users\11942\AppData\Roaming\Typora\typora-user-images\image-20221121195353978.png)

**配置**

* 链接，key和value反序列化，groupId
* 可以订阅多个topic



**消费者消费指定的某一个分区**

![image-20221121200006876](C:\Users\11942\AppData\Roaming\Typora\typora-user-images\image-20221121200006876.png)



一个消费者组订阅一个 topic，一个消费者失败时其他消费者顶上

一个分片只能被同一消费者组中的一个消费者消费，消费者的数量超过分片的数量，部分消费者就会闲置。

可以创建多个消费者组，订阅同一个 topic，多个消费者组之间不受影响



**创建消费者**

**订阅主题**

**轮询**



Kafka 不会像其他 JMS 一样等消费者确认，从偏移量指定的地方处理



我们把更新分区当前位置的操作叫做提交

**提交偏移量**

消费者如何提交偏移量

消费者往_consumer_offset的特殊 topic 里发消息，消息包含每个分区的偏移量，如果消费者崩溃或者有新的消费者加入进来引起再均衡，

**引起的重复消费和消息丢失**

**自动提交**

enable.auto.commit设置为 true，每隔 5s 就把 poll 拉取到的最大偏移量提交上去，





与其他 MQ 比较，Kafka 的优势？

* 吞吐量
* 



**JMS 与 AMQP**

这些标准跟不上 MQ 的发展，实际每个 MQ 有自己的消息模型

队列模型

发布订阅模型



Kafka 与 RocketMQ 的对比

Kafka 中没有队列的概念，与之对应的是分区



**多副本机制**

分区的多个副本中一个是 leader，另外的叫 follower。follower 副本从 leader 副本中拉取消息进行同步，生产者和消费者只跟 leader 通信，副本只是做一个备份。

选举：

follower 中如果和 leader 同步程度达不到要求的参加不了 leader 的选举



分区的好处

* 提高吞吐量
* 提高存储能力



副本的好处：

* 提高容灾能力



**zookeeper 在 Kafka 中的作用**



**Kafka 如何保证消息的消费顺序**

发送顺序

分区



**如何保证消息不丢失**

消息丢失的场景：

消费者：

消费者自动提交偏移量，消费者拉去分区消息后自动提交偏移量，但是此时挂掉，这次来取的消息还没有消费，就会丢失。

关闭自动提交：

真正消费完后再提交，但是消费完后挂掉了，会造成重复消费。

Kafka：

leader 还没有同步消息就挂掉了，解决方案：acks=all



1. 生产者异步发送
2. 生产者







# 面试题



**Kafka零拷贝原理**

将磁盘中的数据发送出去需要经过四次拷贝，零拷贝就是去掉两次多余的拷贝，相对于用户控件来说是零拷贝



**Kafka与ZK的关系，为什么要去掉ZK**

ZK中存储着：

* 服务器节点运行状态，正常工作的BrokerId
* 主题下的每个分区的Leader是哪个BrokerId，哪些可用的BrokerId
* 消费者的Offset信息(新的存储在Kafka中的一个Topic中)



**Kafka基础架构**

Topic：

Partition：一个Topic数据量可能很大，需要分而治之

生产者组：

消费者组：一个Topic分成了多块，就可以有多个消费者一起消费，一个分区只能由同组中的一个消费者消费，所以消费者数量不要多于分区数量

Broker：Kafka服务



**生产者消息发送方式**



**消费者提交offset方式**

自动提交：默认5s提交一次

手动提交：手动提交分为同步提交(提交成功后再消费下一波数据)和异步提交，通常使用手动异步提交

重复消费：消费完的消息没有提交offset，再次进行了消费

消息丢失：还没消费完就提交了offset，然后消费失败了



**offset的存储位置**

0.9之前存储在ZK，0.9之后存储在一个特殊的Topic中，key由topic名+分区+groupId组成(该Topic默认是系统的Topic，不能被消费者消费)



**指定offset位置进行消费**

除了从头或者从最新消费，消费者还可以指定某个分区的offset进行消费



**精确一次性消费**



**消息积压**

增加分区并同时增加消费者数量





**按照指定时间进行消费**

获得对应时间的offset，再从指定的offset进行消费



**提高生产者吞吐量**

batch.size

linger.ms

compression.type，对消息进行压缩

RecordAccumulator，增大缓冲区大小



**数据的可靠性**

ACK应答机制，默认为ALL(等同于-1)

-1，所有ISR收到消息后

分区副本大于等于2

ISR副本数量(可工作的副本于Leader的集合)大于等于2



**数据重复性**

ACK=-1，消息同步后，应答失败了(比如Leader挂了)，生产者会重新发送.

***幂等性***

幂等性：无论消费者想Broker发送了多少次重复的消息，Broker只会持久化一条

使用：生产者开启，参数：enable.idempotence=true(默认开启)

幂等性的判断依据：\<PID,Partition,SeqNumber>，但只能保证一次会话(重启后重新分配PID)中的一个分区不会重复

PID,生产者ID号，开启幂等性的Producer在 "初始化" 的时候会被分配一个PID

***幂等性+事务***

TransactionCoordinator事务协调器，每个节点都有事务协调器，通过事务ID算出使用哪个Broker的事务协调器(hash%分区数量)

_transaction_state分区：存储事务的特殊主题

生产者使用事务前必须自定义一个唯一的事务ID(替代了之前的PID，防止重启时重新获得PID )，即使客户端挂了，重启后也能继续执行后续事务(已经持久化到_transaction_state分区)。 

![image-20221116165549103](..\image\Kafka-幂等+事务)

生产者开启事务，发送消息，提交事务或异常时终止事务



**有序性**

能保证单分区内有序

如何产生乱序的：

生产者默认允许缓存5个未应答的请求(发送到Broker还没获得应答时可以接着发，最多还能发5个批次的数据)

未开启幂等性时InFlightRequests=1，发送成功后才能发下个批次的

开启幂等性时允许InFlightRequests小于等于5，Kafka会保证最近5个消息的顺序(通过幂等性的SeqNumber，SeqNumber时单调递增的，如果发现SeqNumber有问题会重新排序再写入磁盘)



**Leader选举流程**



**分区怎么分配的**

分区的数量大于Broker的数量怎么分配



**文件存储机制**

一个分区对应一个日志文件存放消息，消息不断追加到末尾，为防止log过大，Kafka采用了分段和索引机制，将每个Partition分成多个Segment，每个segment包含index文件(偏移量索引文件)，log文件和timeindex文件(判断日志保存多久)，这些文件位于同一个文件夹下，文件夹的命名规则是topic名称+分区号。一个Topic分成多个Partition，一个Partition分成多个Segment，每个segment下的文件名名字以偏移量开始。

index文件不记录每个消息的位置，每写入4k消息记录一次



**文件清楚策略**

默认基于时间，保存7天(以segment中记录的最大时间戳稳准)

基于大小默认关闭，超过大小(log整体的大小)时删除最早的segment

清除策略有两种：

* delete
* compact：对于相同key，只保留最新的值，所以只适合特殊的场景，如key时UID，Value是用户资料





**Kafka的高效读写**

* 分布式集群，分区技术，可以同时有多个生产者消费者操作一个Topic
* 稀疏索引，快速定位消息在日志中的文件
* 顺序写磁盘的速度要比随机写快
* 零拷贝技术



**零拷贝**

将磁盘中的内容发送到远程服务器正常情况需要经过四次copy

1. 将磁盘中的消息copy到内核缓冲区
2. 将内核缓冲区的消息copy到用户空间的缓冲区
3. 将用户空间的消息拷贝到内核空间Socket缓冲区
4. 将消息拷贝到网卡缓冲区进行发送

零拷贝并不是不拷贝而是相对于用户态来说不进行拷贝

1. 将磁盘中的消息copy到内核空间的Socket缓冲区
2. 将Socket缓冲区的消息发送到网卡缓冲区











