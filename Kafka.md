# Kafka



场景

统计

centipede 更新事件







安装

安装 ZK



Docker 安装

https://www.cnblogs.com/yxlblogs/p/10115672.html



docker run -d --name kafka --publish 9092:9092 --link zookeeper --env KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181 --env KAFKA_ADVERTISED_HOST_NAME=10.83.49.158 --env KAFKA_ADVERTISED_PORT=9092 --volume /etc/localtime:/etc/localtime wurstmeister/kafka:latest







干嘛的

组成

安装

使用

业务场景



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



批次：

为提高效率，消息分批次写入 Kafka，同一个批次 Topic 和分区相同



**schema**

消息是晦涩难懂的字节数组，可以使用额外的结构来定义消息的内容，



**Topic**

对消息进行分类，一个 Topic 可以分成多个分区，消息以追加的方式写入分区，同一分区才能保证消息的顺序。key 或分区器来实现分区





**生产者**































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



如何创建

如何发送

如何处理返回的错误

配置

Kafka 不局限于 Java，它提供了二进制连接协议，只要想 Kafka 的端口发送适当的字节序列，就可以发送和读取数据









多样的场景对应多样的需求

* 是否允许消息丢失
* 是否允许消息重复
* 有没有延时和吞吐量的要求



向 Kafka 发送消息的主要步骤

1. 生产者创建 ProducerRecoder，包含 Topic，[Key]，[Parttition]，value
2. 序列化成字节数组
3. 通过分区器，生产者知道了将消息发送到哪个分区，然后将消息添加到对应的批次，有独立的线程会将批次中的消息发送到同一主题的同一分区
4. Broker 收到消息，成功写入 kafka 后返回一个 RecordMetaData 对象，包含分区和偏移量等信息，如果失败返回错误，生产者收到错误会进行重试，几次都失败后就返回错误信息



**Kafka 生产者 3 个必要的属性**

bootstrap.servers

指定 broker 的地址清单，不需要包含所有的 broker，生产者或根据给定的 broker 获得其他 broker，建议至少配两个，防止一个宕机

key.serializer

key 的序列化，虽然不一定使用 key，但是必须设置

value.serializer



**发送消息的三种方式**

发送并忘记：只管发送，不关心结果，可能存在消息丢失风险

同步发送：发送并调用 get()等待是否成功,失败是 get 会发生异常

异步发送：发送消息并指定回调函数，返回时调用该函数



无法通过重试解决的错误(如消息太大)不会进行错误重试，会直接发送错误



**其他配置**

**其他参数**

ack

必须有多少个分区副本收到消息后，生产者才任务消息写入成功

0 表示不关心，不等待服务器的相应

1 集群首领节点收到消息后

all 所有



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

只有在不改变分区数量的情况下，key 与分区的映射才不会改变，如果要使用 key，那么最初就应该吧分区规划好

**自定义分区器**

需要将某些消息分配到单独的分区



# 消费者

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









































