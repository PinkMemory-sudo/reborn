NIO





三个核心组件：

Channel

Buffer

Selector

其他组件：

Pipe

FileLock







* NIO与传统的IO的区别



NoBlocking

分组IO

更加高效？

1. 传统IO面向流，NIO面向缓冲区

建立目标文件与应用程序之间用于传输数据的数据流，IO直接面向字节数组的流动，**单向**的，分输入流和输出流。

建立目标文件与应用程序之间用于传输数据的通道，通道本身不进行数据传输，数据的传输依赖于缓冲区，缓冲区

是**双向**的

2. 针对于网络IO，IO是阻塞的，NIO是非阻塞的







缓冲区，根据数据类型的不同，提供了不同类型的缓冲区(boolean除外)

byte,short,int,long,float,double，都集成buffered

通过allocath获得缓冲区，最常用的树byte，分配一个指定大小的缓冲区来存储数据。put,get。

**缓冲区的4个核心属性**

capacity，容量，缓存区本质就是数组，所以容量声明后不能改变

limit，缓冲区中可以操作数据的大小，limit后面的数据不能进行读写。

如果limit小于0或者大于capacity则抛异常，小于position则limit置为position，小于mark则mark失效

position，缓冲区中正在操作的位置，

position <= limit <= capatity







**Buffer**



flip

反转读写模式，buffer初始的默认模式为写







get获得position的byte并且position自增一

重载的方法：



put将byte写入缓冲区的position



compact

将position和limit之间的byte拷贝到缓冲区的最前面。

从缓冲区写完数据后调用compact



wrap变为阻塞的

position变为0，limit/capacity成为数组的长度，mark失效



slice

```
Creates a new byte buffer whose content is a shared subsequence of this buffer's content.
```

创建一个新缓冲区共享此缓冲区，缓冲区的改变对新缓冲区可见





























# IO与NIO

NIO，异步非阻塞





# Channel

原节点与目标节点的连接，但是不能操作数据，

调用操作系统的IO接口



DMA/Channel

DMA，内核态用户态，

DMA向CPU申请权限，获得授权后建立DMA总线，IO操作全权交给DMA操作

DMA总线过多，造成总线冲突

DMA升级为Channel，变成完全独立的处理器，专门来处理IO操作，不需要CPU请求

所以，面对大量的IO请求是，Channel要比Stream的CPU利用率高。



利用通道来传输缓冲区



Channel的主要实现类

| 实现类              | 描述               |
| ------------------- | ------------------ |
| FileChannel         | 对本地文件进行操作 |
| SocketChannel       | 网络数据的传输     |
| ServerSocketChannel |                    |
| DataGramChannel     |                    |



## 通道与流的区别



**应用程序与磁盘之间的数据写入或者读出，都需要由用户地址空间和内存地址空间之间来回复制数据。**

内存地址空间中的数据通过操作系统层面的IO接口，完成与磁盘的数据存取



通道的主要实现类  

* FileChannel 用于本地文件数据传输  
* SocketChannel 用于网络，TCP 
* ServerSocketChannel 用于网络，TCP
*  DatagramChannel 用于网络，UDP



通道之间的数据传输 

* transferForm() 
* transferTo()

## 获得Channel的三种方式：



1. 支持Channel的类中getChannel方法获得对应的Channel(底层调用的是FileChannelImpl.open,可读不可写)

**本地IO中**

* FileOutputStream/FileInputStream
* RandomAccessFile



**网络IO中**

* Socket
* ServerSocker
* DatagramSocker



2. JDK1.7NIO2中的静态方法Open



2. JDK1.7中NIO2中工具类Files里的newByteChannel





# Buffer







mark

记录当前position的位置



reset

将position置为上一个(即mark标记的)



rewind，重复读

clear，清空缓冲区，三个指针变了，但是缓冲区中的数据还存在。



isDirect

判断是不是直接缓冲区



直接缓冲区与非直接缓冲区







**弊端**

写入到物理内存中的数据Java就操作不了的，什么时候写入到磁盘中，完全由操作系统控制。

**直接建立到物理内存中消耗大？为什么？什么时候用直接缓冲区？**



## MappedByteBuffer



直接缓冲区与非直接缓冲区

内存映射



直接缓冲区与非直接缓冲区

直接缓冲区：

应用程序直接操作物理映射文件，IO操作不用在操作系统和虚拟机之间赋值，提高了读写速度，但是增加内存的消耗，内存的释放只能通过Java的垃圾回收机制来释放，并不是一不使用就回收。

* 通过allocateDirect获得的缓冲区
* 建立在物理内存上



非直接缓冲区：

* 通过allocate分配的缓冲区
* 建立在JVM上



**直接缓冲区会提高效率？**

应用程序和物理磁盘间的数据没有办法进行直接传输，应用程序面向操作系统的IO接口

一般情况下,最好仅在直接缓神区能在程序性能方面带来明显好处时分配它们。



**直接缓冲区效率高的原因**(与间接缓冲区的区别)

磁盘文件->OS->JVM->应用程序

首先会读取到内核的地址空间，然后拷贝到JVM，所以传统的IO和非直接缓冲区浪费了一步拷贝。



**内存映射文件**

直接缓冲区，直接操作OS中的物理内存，磁盘文件读入内存，应用程序直接操作内存。



直接缓冲区通道之间的数据传输



**使用直接缓冲区完成文件复制**





**RandomAccessFile**

随机访问，我们之前创建的文件都是从开头开始的，而RandomAccessFile可以指定读写的位置

既可以读也可以写。

常用方法：

获得RandomAccessFile

`RandomAccessFile(String name, String mode)`

`RandomAccessFile(File file, String mode)`

mode的取值：r、rw、rws、rwd



| 方法                | 描述                                     |
| ------------------- | ---------------------------------------- |
| seek(int index)     | 将指针移动到某个位置开始读写             |
| setLength(long len) | 给写入文件预留空间                       |
| wirteXxx            | 会覆盖掉指针后面的数据                   |
| readLine            | 读一行,意识位置不是0，而是指针所在的位置 |



## 分散(Scatter)和聚集(Gather)



**分散读取与聚集写入**，就是在读写时可以传进去一个Buffer数组

scatter/gather

分散读取:将Channel中的数据分散到各个Buffer中，按照缓冲区的顺序，一次将缓冲区填满

聚集写入:将多个Buffer的数据聚集到Channel中，将缓冲区中的数据依次写入缓冲区



将缓冲区转换为字节数组。







## charBuffer















## 







































# Selector

多用复路器

用于监听多个Channel的事件(连接打开，数据到达)



字符集Charset



**NIO的核心在于网络IO**



传统IO是阻塞的：客户端向服务端发送请求，服务端会一直等待接收客户端数据，服务端的该线程会一直阻塞。

客户端在想服务器发送数据时，数据会首先到达系统中的内核地址空间，线程会阻塞到内核地址空间中有数据，才会将数据拷贝到用户地址空间，读取到程序中。



**NIO的非阻塞式**

关键：选择器

会将通道注册到选择器上，选择器会监控通道的IO事件。IO准备就绪时，选择器才给通道分配线程。



获得网络通道

通道分为本地通道和网络通道





异步读取文件

**Java NIO AsynchronousFileChannel**



使用直接缓冲区完成文件的复制(内存映射文件)



常见错误

`java.nio.channels.NonWritableChannelException`





高危推送

每日向客户推送有高危犯罪倾向的人员。

根据每个客户所订购内容，按照不同策略从多个数据源中使用Fork/Join检索出满足客户要求的高危人员

根据检索出的数据给每个客户生成任务，将数据存入ES中

将生成的任务数据进行加密，打包

根据客户所属的传输通路，将任务传统过FTP协议传输到各个数据中心

客户测的数据解析功能

客户测的任务查看，人员查看，贴吧数据和搜索记录信息查看功能

客户测的高危人员信息批量/全量下载功能(下载内容分收费版/试用版)



疑似甄别

根据警方提供的人员信息，判断人员是否存在某些犯罪倾向

客户传入手机号和犯罪倾向等信息提交疑似甄别任务

判断客户提交的次数和个数是否达到限制

每个人员分割成一个任务进行查询

根据客户提供的条件查询ES，提供两种ES客户端：HTTP/TCP

将查询结果存入ES，内容存入Mongo



智能预警



消息提醒功能

开函提醒，服务过期提醒的自动生成，升级提醒等手动添加

通过邮件发送消息提醒



提升单测

检测程序正确性，单测覆盖率提高到出厂标准











通过鉴权服务，获得所有客户的信息，根据客户所购买的产品推送对应的内容。根据每个客户指定的筛选和排序策略，从PM标注数据，蓝名单数据，全国数据中检索出符合客户要求的高危人员，为每个客户生成一个任务，存入ES中。然后将





















