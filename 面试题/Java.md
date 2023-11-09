# Basic



**==与equals的区别**

==比较的是地址，判断是不是同一个对象

equals没有重写时等价于==，用来比较地址，重写后用来比较对象的内容。常用的String重写了equals方法。



**为什么重写 equals 时必须重写 hashCode 方法**

默认情况下，hashCode是对象在堆中的一个特殊值，而equals比较的则是在堆中的地址，所以无论如何，每个对象的hashcode和equals都不会相同。

对象的hashcode主要用来计算出该对象在hash表中的索引，然后equals来比较两个对象是否相同这样就减少了比较的次数；而如果equals相同，hashcode不同，就会导致相同的元素出现在了hash表的不同位置，

所以必须保证：

**equals相同时，hashCode也一定要相同**。所以重写了equals方法，则生成hashcode的条件也要发生改变。



**为什么Java只有值传递**

值传递就是实参中保存的值(不管是值还是一个地址)，复制给形参，形参对它的值进行修改，实参的值并不会被改变。所以Java中可以改变实参引用的对象的内容，但改变不了它的引用。



**final关键字的作用**

被 final 修饰的变量不可以被改变，  如果修饰引用,那么引用不可变,引用指向的内容可变

被 final 修饰的方法,可以继承但不能重写，JVM 会尝试将其内联,以提高运行效率

被 final 修饰的类不可以被继承(断子绝孙类)



**Java 序列化中如果有些字段不想进行序列化，怎么办**

使用 transient 关键字修饰



**深拷⻉ vs 浅拷⻉**

浅拷⻉:对基本数据类型进行值传递，对引用数据类型进行引用传递般的拷⻉，此为浅拷⻉。

深拷⻉:对基本数据类型进行值传递，对引用数据类型，创建一个新的对象，并复制其内容，此为深拷⻉



**能否重写父类的构造方法**

不能，构造方法不会被继承



**一个无参的构造方法有什么用**

子类默认会调用父类的无参构造方法



**成员变量和局部变量的区别**

* 成员变量声明在类中，可以被修饰符修饰，局部变量声明在方法中，
* 两者存储的位置和声明周期不同
* 成员变量有默认值



**静态方法和成员方法的不同**

* 静态方法属于类，通过类名调用，非静态方法属于某个实例，通过实例名调用

* 非静态方法可以调用静态方法



**什么是多态，Java是如何实现的**

就是指同一个引用类型，使用不同的实例而执行不同的操作

继承，重写，父类引用指向子类的实现



**Java中有没有指针**

有，JVM使用，我们不能用



**四个访问修饰符合访问级别**  

类中的，同包的，子类的，公共的：private，protected，友好的，public



**static都有哪些用法**  

static可以作用于变量,方法，代码块，内部类。

被static修饰，会在类被加载的时候就加载，被所有的对象所共享，在内存中只有一个副本。不依赖于对象，可以直接调用。



**static代码块和普通代码块的区别**

static代码块只在类加载的时候执行一次，普通代码块会在每次创建对象时执行一次



**Object 中有哪些公共方法**

hashCode(),返回对象的hash值，主要为了方便计算散列表的索引

equale()与==是一样的，用来比较地址。子类一般需要重写该方法，来比较内容。

toString()返回对象的文本表达形式

clone()对象的浅拷贝(只有实现了 Cloneable 接口才可以调用该方法)

getClass()获得对象的class

notify()

notifyAll()

wait()

finalize()



**&和&&的区别**

两者都可以进行逻辑与运算，&是按位与。&&具用断路功能，即第一个表达式为false时不在进行逻辑与直接返回



## 变量



**Java 的基础类型和字节大小**

boolean 	    1位

byte			    8位

char			    16位

short	  		16位

int				   32位

long				64位

float				32位

double			64位



**a=a+b与a+=b的区别**

a+=b会将计算结果隐式的转为持有的结果



**char能不能存一个汉字**

可以，Java使用的是unicode编码，一个char占两个字节



**switch 能否作用在 byte、long、string 上** 

switch 可作用在 char、byte、short、int(小于等于int的数字)及他们的包装类上和String



**在调用子类构造方法之前会先调用父类没有参数的构造方法, 其目的是**

创建父类对象



**怎么使用BigDecimal进行浮点型数据运算**

1. 获得BigDecimal对象new BigDecimal(String)获得静态方法BigDecimal.valueOf(Double)
2. 调用加减乘除运算，add,subtract,multiply,divide
3. 返回String或者double



**Match常用方法**

pow，round，abs，random



**if(a+1.1=1.3)行吗**

不要在boolean表达式中用浮点型数据



**char的默认值**

''



**跳出多层循环**

```java
labelName:
......
  break labelName;
```





## 面向对象



**面向对象和面向过程的区别:**

面向过程执行效率更高



**对象实例化的顺序**

* 静态属性静态代码块
* 普通属性，普通代码块
* 构造方法



**static属性为什么不会被序列化**



**代码块与构造函数的执行优先级**

静态代码块>构造代码块>构造方法



**哪些方法不能被重写**

被final修饰的方法，static修饰的方法，或private修饰的方法，跨包的话因为不可见所以也不能重写

构造方法不会被继承，也就不会被重写



**new 一个对象的过程和 clone 一个对象的过程**

* new是java中的关键字。他通过调用类的对应构造方法来创建对象。它会在JVM中重新生成一块空的内存空间，再调用构造函数对对象进行初始化，最后返回对象的地址。
* clone方式需要实现Cloneable接口来重写clone的方法，如果不实现就重写会报java.lang.CloneNotSupportedException的异常。先分配内存，再调用clone方法对原对象进行复制，最后返回新对象地址。
  



**什么是内部类**

定义在其他类或者方法中的类，广泛意义上的内部类一般来说包括这四种：成员内部类、局部内部类、匿名内部类和静态内部类。

内部类编译后生成两个字节码文件:`Outclass.class`,`Outclass$InnerClass.class`



**成员内部类**

编译期会给内部类一个外部类的引用，所以成员内部类可以直接访问外部类的属性和方法(包括private)，如果内部类与外部类的方法或字段名重复时，默认使用的是内部类，通过外部类.this.xxx来调用外部类的成员。外部类需要通过内部类的对象类调用内部类的成员。

成员内部类也可以加private等修饰符



**静态内部类**

被static修饰的内部类，静态内部类只能范围外部类的静态成员，静态内部类不再依赖于外部类。



**创建内部类**

成员内部类依赖外部类

```
1. OutClass.InnerClass innerClass = outClassInstance.new InnerClass();
2. 外部类提供一个获得内部类的方法
```

静态内部类不依赖外部类

```
外部类类名.内部类类名 xxx = 外部类对象名.new 内部类类名()
```



**为什么要使用内部类**

* 要解决一个复杂的问题，并希望创建一个类，用来辅助自己的程序方案。同时不愿意把它公开
* 从某种意义上可以实现多继承



**为什么匿名内部类和局部内部类只能访问局部final变量**

内部类和外部类是同一级别的，不会因为外部类执行完就回收内部类。

当外部类执行完销毁时，可能内部类还没执行完，需要用到外部类的变量。解决办法就是内部类会复制一份外部类中的局部变量来作为内部类的类变量，但是为了与外部类保持一致，就需要使用到final来控制。



**成员内部类和静态内部类的区别**

* 成员内部类依赖外部类，保存的有内部类的引用
* 成员内部类可以直接使用外部类的所有成员，静态内部类只能使用静态成员



**abstract class 和 interface 有什么区别**

由abstract修饰的方法叫抽象方法;由abstract修饰的类叫抽象类。抽象类就是一个还不完善的类

在软件工程中,接口泛指供别人调用的方法或者函数， 在Java语言中,接口是对行为的抽象,是**方法声明的集合**(主要)

* 类用来定义属性和行为，而接口则主要声明类要实现的行为。
* 接口没有构造方法，抽象类中可以定义构造函数(声明通用的构造函数)
* Java8前，接口中都是抽象方法(java8新增default和非抽象方法, jdk9以后可以有private，9之前定义private会编译报错的 )，抽象类中可以有抽象方法，也可以有实现的方法
* 接口中只能由static 和 final 变量 ，不能有其他成员变量
*  从设计层⾯来说，抽象是对类的抽象，是⼀种模板设计，⽽接⼝是对⾏为的抽象，是⼀种⾏ 为的规范 
*  Jdk 9 在接⼝中引⼊了私有⽅法和私有静态⽅法 



**接口的变化**

* Java8之前，接口中的方法被`public abstract`隐式修饰，变量被`public static final`修饰
* Java8引入了默认方法
* Jdk 9 在接口中引入了私有方法和私有静态方法。



**方法重载的规则**

同名不同参，其他的异常，返回值等无关



## 字符串



**判断一个String中只包含数字**

```
s.matches("[0-9]{1,}")
```



**怎么指定字符串的编码形式**

new String(s.getBytes(),"编码")



**String、StringBuilder、StringBuffer 区别**  

String是不可变字符，都会生成一个新对象(会转成StringBuilder，然后append，再toString)，所以在进行大量字符串拼接是会产生许多对象

StringBuilder只产生一个对象，然后一直append来拼接

StringBuffer与StringBuilder相同，但是StringBuffer是线程安全的



**String str="hello world"和String str=new String("hello world")的区别**

使用""创建的字符串存储在堆中的字符串常量池中，相同的内容只会存储一份。new String()，通过new创建出来的字符串存储是在堆内存中，不会去检查是否存在。

String str="hello world"， 在编译期，JVM会去常量池来查找是否存在“abc”，如果不存在，就在常量池中开辟一个空间来存储“abc”；如果存在，就不用新开辟空间。然后在栈内存中开辟一个名字为str1的空间，来存储“abc”在常量池中的地址值。

String str=new String("hello world")：在编译阶段JVM先去常量池中查找是否存在“abc”，如果过不存在，则在常量池中开辟一个空间存储“abc”。在运行时期，通过String类的构造器在堆内存中new了一个空间，然后将String池中的“abc”复制一份存放到该堆空间中，在栈中开辟名字为str2的空间，存放堆中new出来的这个String对象的地址值。

 


**String a = "hello2"; String b = "hello" + 2; System.out.println((a == b));会输出什么**

true， "hello" + 2会在编译时被jvm优化成"hello2"，所以a和b是同一个对象



**String a = "hello2"; 　 String b = "hello";    String c = b + 2;    System.out.println((a == c));的输出结果**

false 因为c是间接相加的，jvm不会对引用变量进行优化



**集合,数组转字符串**

String.join(delimiter,elements)



**字符集和编码方式**

字符集就是字符的集合，编码方式是即可理解为定义在字符集上的映射规则

对于unicode字符集，有utf8,utf16,utf32等多种编码方式，对于其他字符集本身就既是字符集又是编码方案



**null转String**

* (String) null 返回null
* String.valueOf(null) 返回字符串"null"
* ""+null 返回字符串null
* null.toString NPE



**String为什么要设计成不可变**

* 字符串常量池：相同字符串可以共用，谁也不能改变



**String为什么要从char[]变成byte[]**

节省占用的内存

字符占两个字节，JDK9后会先判断用不用一个字节，降低内存占用，减少GC，不过中文的字符串用的utf16占两个字节，所以区别不大



**String存在哪**

String对象存在堆中，String字符串存在常量池



## 日期



**SimpleDateFormat为什么是线程不安全的**

其中存放日期数据的变量都是线程不安全的。



**怎么计算两个日期的间隔**

两个日期间的比较可以使用isAfter等，而计算间隔可以使用ChronoUnit.DAYS/hour/minutes等的between方法。





## 异常



**异常的体系结构**

Throwable

Exception，error

Exception:Check  UnCheck



**throw 和 throws 的区别**

throw用来抛出一个异常对象，作用在方法内部

throws表示出现异常的一种可能性，作用在方法名后



**try-catch-finally**

try 块： ⽤于捕获异常。其后可接零个或多个 catch 块，如果没有 catch 块，则必须跟
⼀个 finally 块。
catch 块： ⽤于处理 try 捕获到的异常。
finally 块： ⽆论是否捕获或处理异常， finally 块⾥的语句都会被执⾏。当在 try 块或
catch 块中遇到 return 语句时，执行完 return 中的语句后不直接返回，而是执行 finally 语句块的代码，finally 中的 return 会覆盖调 try-catch 中的 return。

​        

 **Java 中怎么创建一个不可变对象** 

* 私有化属性(最好在加上final)
* 不提供修改变量的共有方法
* 确保始终返回该字段的副本，而不返回真实对象实例。

​        

 **Java 中 ++ 操作符是线程安全的吗**

​         

**final、finalize 和 finally 的不同之处**  

finalize 是 Object 类的一个方法，在垃圾收集器执行的时候会调用被回收对象的此方法， 可以覆盖此方法提供垃圾收集时的其他资源回收，例如关闭文件等，jdk9以后这个方法就过时了



## 反射



**什么是反射，反射的作用**  

反射是指可以访问、检测和修改其本身状态或行为的一种能力



**反射都可以获得什么信息**

* getMethod(name,param),根据名字获得方法，filed相同

* getMethods，获得说有公共方法(包含父类的和Object)，field相同
* getDeclaredMethods，获得自己的所有方法(包含private，不包含父类和Object)，field相同
* 通过Method和Field获得上面的注解
* 获得私有方法需要getDeclaredMethod(),执行private方法需要setAccessible(true)



**获取 Class对象 的三种方式**       

* 通过实例获得：对象.getClass()方法
* 通过类名获得：类名.class
* 通过Class获得：Class.forName("xxx")



反射中，Class.forName()和 ClassLoader.loadClass()的区别？





## IO



**Java有几种类型的流**

按照流的流向分，可以分为输入流和输出流;
按照操作单元划分，可以划分为字节流和字符流;

主要都是从InputStream/Reader，OutputStream/Writer派生来的



**字符流和字节流的区别**

* 字节流操作单位是1个字节，字符流操作单位是2字节
* 字节流是二进制流，字符流使用缓冲，在字节流的基础上按照字符编码处理，，处理的是char



**既然有了字节流,为什么还要有字符流**

字符流是由Java虚拟机将字节转化为2个字节的Unicode字符为单位的字符而成的。

字节流提供了处理任何类型的IO操作的功能，但它不能直接处理Unicode字符。



**使用IO的最佳实践**

* 使用有缓冲的IO类
* 使用NIO或者NIO2
* 在finally中关闭流或者使用try-with-resource



**UNIX 系统下的 5 种 IO 模型**

**同步阻塞 I/O**、**同步非阻塞 I/O**、**I/O 多路复用**、**信号驱动 I/O** 和**异步 I/O**。



**Java 中的三种 IO 模型：BIO,NIO,AIO的区别**

BIO： 同步阻塞 I/O 模式，就是我们平常使用的IO，使用简单，并发低。

NIO： 同步非阻塞I/O模式，通过channel传输，实现了多路复用，对于高负载、高并发的（网络）应用，应使用 NIO

AIP： NIO2，异步非阻塞的 IO 模型，基于事件和回调机制，应用不广

同步，发起 read 后一直阻塞，直到将内核态的数据复制到用户态

同步与异步，阻塞与非阻塞的区别：IO包括IO的请求以及实际IO，阻塞和非阻塞发生在IO的请求，同步和异步发生在IO的操作。



```
BIO (Blocking I/O): 同步阻塞 I/O 模式，数据的读取写⼊必须阻塞在⼀个线程内等待其完
成。在活动连接数不是特别⾼（⼩于单机 1000）的情况下，这种模型是⽐ 不错的，可以
让每⼀个连接专注于⾃⼰的 I/O 并且编程模型简单，也不⽤过多考虑系统的过载、限流等问
题。线程池本身就是⼀个天然的漏⽃，可以缓冲⼀些系统处理不了的连接或请求。但是，当
⾯对⼗万甚⾄百万级连接的时候，传统的 BIO 模型是⽆能为⼒的。因此，我们需要⼀种更⾼
效的 I/O 处理模型来应对更⾼的并发量。
NIO (Non-blocking/New I/O): NIO 是⼀种同步⾮阻塞的 I/O 模型，在 Java 1.4 中引⼊了
NIO 框架，对应 java.nio 包，提供了 Channel , Selector，Buffer 等抽象。NIO 中的 N 可以
理解为 Non-blocking，不单纯是 New。它⽀持⾯向缓冲的，基于通道的 I/O 操作⽅法。
NIO 提供了与传统 BIO 模型中的 Socket 和 ServerSocket 相对应的 SocketChannel 和
ServerSocketChannel 两种不同的套接字通道实现,两种通道都⽀持阻塞和⾮阻塞两种模式。
阻塞模式使⽤就像传统中的⽀持⼀样，⽐ 简单，但是性能和可靠性都不好；⾮阻塞模式正
好与之相反。对于低负载、低并发的应⽤程序，可以使⽤同步阻塞 I/O 来提升开发速率和更
好的维护性；对于⾼负载、⾼并发的（⽹络）应⽤，应使⽤ NIO 的⾮阻塞模式来开发
AIO (Asynchronous I/O): AIO 也就是 NIO 2。在 Java 7 中引⼊了 NIO 的改进版 NIO 2,它
是异步⾮阻塞的 IO 模型。异步 IO 是基于事件和回调机制实现的，也就是应⽤操作之后会直
接返回，不会堵塞在那⾥，当后台处理完成，操作系统会通知相应的线程进⾏后续的操作。
AIO 是异步 IO 的缩写，虽然 NIO 在⽹络操作中，提供了⾮阻塞的⽅法，但是 NIO 的 IO ⾏
为还是同步的。对于 NIO 来说，我们的业务线程是在 IO 操作准备好时，得到通知，接着就
由这个线程⾃⾏进⾏ IO 操作，IO 操作本身是同步的。查阅⽹上相关资料，我发现就⽬前来
说 AIO 的应⽤还不是很⼴泛，Netty 之前也尝试使⽤过 AIO，不过⼜放弃了。
```







**Files常用的方法**

| 方法                | 描述             |
| ------------------- | ---------------- |
| Files.exists()      | 是否存在         |
| Files.createFile()  | 创建文件         |
| Files.creatDirectoy | 创建文件夹       |
| Files.delete()      | 删除文件或文件夹 |
| Files.copy()        | 复制文件         |
| Files.move()        | 移动文件         |
| Files.size()        | 查看文件个数     |
| Files.read()        | 读               |
| FIles.write()       | 写               |



2. 字节流与字符流怎么选
3. 缓冲区的作用
4. 缓冲流与缓冲区的区别
5. 字节流和字符流的区别
6. 什么是Java序列化，如何实现Java序列化
7. 什么是节点流和处理流
8. 各个read方法的含义
9. 字节数组流
10. read()与read(byte[] b)区别



**删除文件夹和文件**

文件夹中有文件/文件夹



**Log4j对程序有影响吗**

将日志记录到本地或者数据库需要消耗IO，频繁操作，会对系统性能产生影响



## 注解



## 加解密

* MD5
* 



## **正则**



**判断一个字符串是数字**





# 容器



**List,Set,Map三者的区别**

* List : 存储的元素是有序的、可重复的。 
* Set : 存储的元素是无序的、不可重复的。
* Map: 使用键值对(kye-value)存储，可以不能重复，value可以重复



**类型擦除**

泛型提供了编译时类型安全检测机制，Java 的泛型是伪泛型，这是因为 Java 在编译期间，所有的泛型信息都会被擦掉，通过发射可以添加不同类型的值



## List



**Arraylist 与 LinkedList 区别?**

ArrayList的***数据结构***是数组，LinkedList的数组结构是链表，所以两者的主要区别就是数组和链表的区别。

ArrayList适合通过下标进行***随机访问***，LinkedList适合频繁插入删除的情况。

ArrayList需要连续的空间，不够时需要扩容，重新分配空间，LinkedList不用。

内存***空间***占用：ArrayList 的空间浪费主要体现在在 list 列表的结尾会预留一定的容量空 间，而 LinkedList 的空间花费则体现在它的每一个元素都需要存放直接后继和直接前驱以及数据



 **ArrayList 的扩容机制**

默认的初始容量是10(最小)

第一次调用add方法添加元素，先取指定的容量和默认容量中最大的那个进行初始化。之后调用add方法，当容量不够时，调用grow方法进行扩容，新容量等于`oldCapacity + (oldCapacity >> 1)`,也就是就容量的1.5倍，然后判断新容量与需要的容量的大小。



**ArrayList 与 Vector 区别**

ArrayList和Vector都是List的实现类主要区别在与Vector是线程安全的，两者的扩容机制也有所区别



**怎么创建一个只读的容器**

调用Collections.unmodifiableCollection(collections s)创建



**多线程场景下如何使用ArrayList**

ArrayList是非线程安全的，需要使用Collections的syschronizedList转为安全的list



**Comparable 和 Comparator接口的区别**  

这两个接口主要用来对集合进行排序。

实现Comparable接口的类，可以直接用来排序。通过Collections.sort()来排序。

而没有实现Comparable接口的类要想排序，需要定义一个比较器。通过list.sort,传入一个Comparator来排序。

Comparable实现compareTo来定义与跟一个对象的比较方法，当前对象大时返回返回值大于0，相等时返回0。

Comparator实现compare方法定义两个对象的比较。

第一个参数-第二个参数为升序

Java中  String、Byte、Char、Date 等大量的类都实现了Compareable接口。



**边循环边删除的问题**

* for循环，删除后元素前移
* 增强for，ConcurrentModificationException
* 使用迭代器的remove





## Set



**set怎么保证不重复的**

会先计算对象的 hashcode 值来判断对象加入的位置，同 时也会与其他加入的对象的 hashcode 值作比􏰀，如果没有相符的 hashcode ， HashSet 会假设对象没有重复出现。但是如果发现有相同 hashcode 值的对象，这时会调用 equals() 方法来检查hashcode 相等的对象是否真的相同。如果两者相同， HashSet 就不会让加入操作成功。



**HashSet 的底层实现是什么**  

HashMap，用的都是HashMap的方法



**为什么重写 equals 时必须重写 hashCode 方法**

默认情况下，hashCode是对象在堆中的一个特殊值，而equals比较的则是在堆中的地址，所以无论如何，每个对象的hashcode和equals都不会相同。对象的hashcode主要用来计算出该对象在hash表中的索引，然后equals来比较两个对象是否相同这样就减少了比较的次数；在set中，会先根据hashcode计算出索引比较是否相同，有的还在调用equals比较对象是否相同来保证元素不重复，如果equals相同但是hashcode不同，可能计算出来的索引不同，就不能保证元素不重复。

所以必须保证：

equals相同时，hashCode也一定要相同。所以重写了equals方法，则生成hashcode的条件也要发生改变。



**HashSet 和 TreeSet 有什么区别**

HashSet通过HashMap实现，TreeSet通过TreeSet实现

存入HashSet需要重新HashCode和equals

存入TreeSet需要实现Comparable



**ThreeSet 红黑树**

红黑树是一种特殊的二叉查找树，每个节点都有颜色

* 保证根节点和叶子节点都是黑色
* 一个节点如果是红色，则它的子节点必须是黑色
* 每个节点到达叶子节点所需要经过的黑色节点相同，相对接近平和二叉树
* 添加删除后都要旋转保持特性





**Iterator 和 ListIterator 的区别是什么？**

* Iterator 可用来遍历 Set 和 List 集合，但是 ListIterator 只能用来遍历 List
* Iterator 对集合只能是前向遍历，ListIterator 既可以前向也可以后向。
* ListIterator 实现了 Iterator 接口，并包含其他的功能，比如：增加元素，替换元素，获取前一个和后一个元素的索引，等等。



**Iterator   遍历时修改？**

迭代器在遍历的时候会设置一个modCount，内容有变化就会修改modCount的值，每当使用hasNext/next时都会检测该值是否发生变化。解决方案：同步



## Map



**Hashtable 和 Hashmap 的区别**


* 安全性：HashMap 是非线程安全的， HashTable 是线程安全的,因为 HashTable 内 部的方法基本都经过 synchronized 修饰
* 效率：HashTable虽然线程安全，但是效率比较低，基本不用
* null值：hashmap的key允许有一个null，hashtable不允许
* 初始容量和扩容机制：HashMap默认厨师容量16，每次扩大到原来的2倍，hashtable默认容量11，每次扩大到原来的2n+1
* 底层数据结构：1.8后，Hashmao加入了红黑树，hashtable没有



**HashMap实现原理**

JDK1.8之前的数据结构是数组+链表，JDK1.8之后是数组+链表+红黑树，当一个链表的长度达到阈值8时，如果当前数组的长度小于64，就会先进行扩容，否则就把链表转成红黑树



**容量为什么是2的幂次方**

需要 hash 对数组长度取模来求出下标

当大是2的幂次方时，求余操作求可以通过(lengh-1)$hash来操作，这样每次根据Hash计算位置时效率就高得多了



**HashMap怎么保证容量是2的倍数的**



**为什么是0.75**



**为什么是8**

泊松分布



**HashMap 多线程操作导致死循环问题**

https://coolshell.cn/articles/9606.html

并发下的Rehash 会造成元素之间会形成一个循环链表。JDK1.8修复了这个问题，但是不推荐多线程使用HashMap。

出现的原因：JDK1.7采用头插法，因为插入速度快，新插入的元素最可能先被使用，JDK1.8后采用了红黑树，提高了查询速度，使用尾插法进行插入。在多线程的情况下



**ConcurrentHashMap 和 Hashtable 的区别**

两者实现线程安全的方式不同

***底层数据结构***：jdk1.8之前ConcurrentHashMap使用的是分段数组+链表来实现，jdk1.8开始，数据结构与jdk1.8的HashMap结构相同，采用CAS和Synchronized来实现线程安全。Hashtable的数据结构与jdk1.7的hashMap相似

***实现线程安全的方式***: 在 JDK1.7 的时候， ConcurrentHashMap (分段锁) 对整个桶数组进行了分割分段( Segment )，每一把锁只锁容器其中一部分数据，多线程访问 容器里不同数据段的数据，就不会存在锁竞争，提高并发访问率。 到了 JDK1.8 的时候已经 摒弃了 Segment 的概念，而是直接用 Node 数组+链表+红黑树的数据结构来实现，并发 控制使用 synchronized 和 CAS 来操作。虽然在 JDK1.8 中还能看到Segment 的数据结构，只是为了兼容旧版本。hashtable使用 synchronized 来保证线程安全，效率非常低下



**ConcurrentHashMap线程安全的具体实现方式/底层具体实现**

*Jdk1.7*

是分成多个segment，每个segment配一把锁

*Jdk1.8*

ConcurrentHashMap 取消了 Segment 分段锁，采用 CAS 和 synchronized 来保证并发安全。synchronized 只锁定当前链表或红黑二叉树的首节点，锁的粒度更小，这样只要 hash 不冲突，就不会产生并 发，效率又提升 N 倍。



**TreeMap、HashMap、LindedHashMap 的区别**   

LinkedHashMap是HashMap的子类，多了一个链表用来保证顺序。



**HashSet、LinkedHashSet 和 TreeSet 三者的异同**

HashSet 的底层是 HashMap ，线程不安全的，可以存储 null 值;

LinkedHashSet 是 HashSet 的子类，能够按照添加的顺序遍历;

TreeSet 底层使用红黑树，能够按照添加元素的顺序进行遍历，排序的方式有自然排序和定制排 序



## Stack



## Queue





# 多线程

https://mp.weixin.qq.com/s/3istnkJ3MMYQnvbtTsdxmA

Runnable

Callable

Volatile

Synchronized

AQS

CAS

线程池

CyclicBarrier

CountDownLatch

Volatile与CAS结合保证原子性

CopyOnWriteArrayList

ConcurrentModificationException

dump文件

1. 获取到线程的pid，可以通过使用jps命令，在Linux环境下还可以使用ps -ef | grep java
2. 打印线程堆栈，可以通过使用jstack pid命令，在Linux环境下还可以使用kill -3 pid

Thread.sleep(0)的作用是什么：*触发操作系统立刻重新进行一次CPU竞争*

ReadWriteLock

读写共享，写锁独占，读读不互斥，读写，写读，写写才互斥

CopyOnWriteArrayList

就是平时查询的时候，都不需要加锁，随便访问，只有在更新的时候，才会从原来的数据复制一个副本出来，然后修改这个副本，最后把原数据替换成当前的副本。修改操作的同时，读操作不会被阻塞，而是继续读取旧的数据。

**UncaughtExceptionHandler**





















**进程和线程的区别**

* 进程是资源分配(内存，文件等)的最小单位，线程是CPU调度的最小单位。

* 一个进程可以有多个线程，共享进程的资源。所以线程执行时一般都要进行同步和互斥。
* 线程切换消耗的资源要比进程小得多，线程也被成为轻量级进程



**并发和并行的区别**

并发是一段时间内执行多个任务

并行是同一时间内执行多个任务



**如何停止正在运行的线程**

* stop方法，已经等废弃，可能会造成数据不一致问题
* interrupt方法，标记当前线程状态，并不会停止线程
* 



**为什么要使用多线程**

* 线程的切换成本远小于进程，多核 CPU 时代意味着多个线程可以同时运行，这减少 了线程上下文切换的开销。

* 为了提高CPU的利用率，减少程序运行时间，提高系统整体的并发能 力以及性能。。



**并发的三要素**

* 原子性
* 可见性
* 有序性：程序按照代码的顺序执行



**什么是上下文切换**

多线程通常是线程数大于CPU数，一个CPU来回切换来执行多个线程。当一个线程执行完CPU时间段切换到另一个线程前，会先将自己的当前状态先保存起来，等再次切换回这个任务时在把状态加载出来接着执行。

上下文切换通常是计算密集型的，也就是他要占用相当比例的处理器时间。Linux 相比与其他操作系统(包括其他类 Unix 系统)有很多的优点，其中有一项就是，其上下文 切换和模式切换的时间消耗非常少。



**多线程可能带来什么问题**

并发编程的目的就是为了能提高程序的执行效率提高程序运行速度，但是并发编程并不总是能提高程序运行速度的，而且并发编程可能会遇到很多问题，比如:内存泄漏、上下文切换、死锁 。



**创建线程的方式**

* 继承Thread
* 实现runnable
* 实现callable



**FutureTask 对象** 

实现了Future和Runnable，即可以作为任务，又能获得任务的执行结果



 **实现 Runnable 接⼝和 Callable 接⼝的区别** 

* Runnable 接⼝不会返回结果或抛出检查异常，但是 Callable 接⼝可以。
* Runnable执行run方法，Callable执行call方法 



**什么是守护线程**

 daemon thread ，服务其他线程的线程，当其他线程都停止守护线程才会停止。JVM退出时不会关心有没有守护线程正在运行。



**产生死锁的四个条件**

互斥条件：该资源任一时刻只能有一个线程占用

请求与保持条件：一个进程因请求资源而阻塞时，不释放自己一获得的资源

不剥夺条件：线程已经获得的资源不可剥夺，只有自己使用完毕后才释放资源

循环等待条件：若干进程间形成一种头围相连的循环等待条件



**如何避免死锁**

破坏行程死锁的四个条件中的任意一条就可以：请求资源时一次请求全部的资源，没有得到时把自己已有的资源释放掉。

请求与保持条件：一次申请全部资源，这样就不会因某个资源被其他线程占用而阻塞

不可剥夺条件：如果没有申请到其他线程占用的资源时，把自己占用的那部分释放掉



***线程的6种状态***

| 状态          | 描述                                                         |
| ------------- | ------------------------------------------------------------ |
| NEW           | 新建状态                                                     |
| RUNNABLE      | 可运行状态，包含READY和RUNNING                               |
| BLOCKED       | 阻塞状态，线程发起阻塞的I/O操作或者申请其他线程占用的资源时。阻塞状态不会占用CPU |
| WAITING       | 等待状态，线程执行了wait或者join状态，执行notify或者加入的线程执行完后进入RUNNABLE |
| TIMED_WAITING | 与WAITING状态类似，但是指定的有时间，超时自动转RUNNABLE      |
| TERMINATED    | 线程运行完之后                                               |



***线程的生命周期***

| 声明周期 | 描述                      |
| -------- | ------------------------- |
| 新建     | 刚被创建                  |
| 就绪     | 调用start()后             |
| 运行     | 获得CPU执行权后           |
| 阻塞     | 阻塞和wait状态            |
| 死亡     | 执行完run方法或者异常退出 |



**sleep方法和wait方法的异同**

* 都会是线程进入阻塞状态，sleep不会释放锁，wait会释放锁。
* wait使用前提是先获得锁，通过锁调用wait方法

* sleep到时间后继续执行wait() 方法被调用后，线程不会自动苏醒，需要别的线程调用同一个对象上的 notify() 或 者 notinotify动苏醒。



**run方法和start方法的区别**

调用 start() 方法，会启动一个线程并使线程进入了就 绪状态，当分配到时间片后就可以开始运行了。

直接执行 run() 方法，会把 run() 方法当成一个 main 线程下的普通方法去执行，并不会在某个线程中执行它



**使用wait和notify写一个生产者消费者代码**



**进行对线程开发时，你有哪些最佳实践**

* 给线程命名，有助于调试
* 最小化同步的范围
* 使用更高层次的开发工具BlockQueue等而不是wait和notify
* 优先使用并发集合而不是同步
* 尽量把阻塞型的IO密集任务，转成CPU密集任务，这样你只需要少量线程也可以获得很高的吞吐量。这就是为何select、poll 、epoll、nginx可以用很少的线程可以获得极大吞吐量的原因。 



**线程阻塞有哪些原因**

* IO会阻塞
* 时间片用完
* 没获得锁



**线程同步的方法**

* wait，notify，notifyAll
* synchronized
* lock



**sleep和yield方法的区别**

* yield方法会放弃CPU执行权，进入就绪态
* sleep会进入休眠

  

**怎么唤醒一个阻塞的线程**

如果是wait、sleep，join

IO阻塞无能威力



**线程调度器和时间分片**

线程调度器是操作系统的服务，负责为Runnable的线程根据优先级和等待时间分配CPU时间。不受JVM的限制，所以Java设置的优先级不一定有用。



**Java怎么使用CAS**



**不可变对象对多线程的影响**

不需要进行额外的同步手段，提高了代码的执行效率。



**Linux获得那个线程使用CPU时间最长**

1. 获得项目的pid，jps或者ps -ef | grep java
2. top -H -p pid



**停止一个正在运行的线程**



**线程之间如何通信**

即线程之间如何交换信息。一般通过共享内存和消息传递解决。

Java的并发采用的是共享内存模型，整个通信过程对我i们来说是透明的。



**线程运行是发生异常会怎么办**

如果没有捕获，该线程将会停止。处理未捕获异常？



线程的dump文件



**servlet是线程安全的吗**

不是，它是单例多线程



**线程间的通信**





## 线程池



**线程池的优点，为什么要使用线程池**

* 重复利用，减少线程创建和销毁的开销
* 提高响应速度
* 可以控制并发量，防止无限创建线程



**线程池的五种状态**

* 线程池处在RUNNING状态时，能够接收新任务，以及对已添加的任务进行处理
* 线程池处在SHUTDOWN状态时，不接收新任务，但能处理已添加的任务
* 线程池处在STOP状态时，不接收新任务，不处理已添加的任务，并且会中断正在处理的任务
* 当所有的任务已终止，ctl记录的”任务数量”为0，线程池会变为TIDYING状态。
* 线程池彻底终止，就变成TERMINATED状态。



**线程池(ThreadPoolExecutor)的7个参数**

| 参数                     | 说明                                                         |
| ------------------------ | ------------------------------------------------------------ |
| corePoolSize             | 线程池要保持的线程的数量                                     |
| BlockingQueue            | 在线程数达到最大时，就会存放在队列中(runnable通过execute提交的) |
| maximumPoolSize          | 线程池中最多可以保存的线程数                                 |
| RejectedExecutionHandler | 线程数已经达到最大，并且队列也满了，对新提交的任务怎么拒绝   |
| keepAliveTime            | 大于核心线程数时，如果执行时间内没有分配到任务就会清掉       |
| TimeUnit                 | keepAliveTime的单位                                          |
| ThreadFactory            | 线程池用它来创建线程                                         |



**线程池的4种拒绝策略**

| 拒绝策略             | 说明                                                         |
| -------------------- | ------------------------------------------------------------ |
| AbortPolicy          | 抛弃任务,直接抛出 RejectedExecutionException 异常阻止系统正常运行 |
| DiscardPolicy        | 直接抛弃任务，不抛出异常                                     |
| DiscardOledestPolicy | 丢弃掉最先入队的，重新尝试添加到队列中                       |
| CallerRunsPolicy     | 调用者自己去执行任务                                         |

   

**最大线程数设置过大会有什么后果**

* 线程时需要内存空间的，创建的线程过多就会占用过大的内存空间
* 线程上下文切换需要消耗时间，线程数太多而进行频繁切换也会浪费时间
* netty的建议是设置为2倍的cpu核心数。 



**线程池的阻塞队列有哪些**

LinkedBlockingQueue无界

DelayedWorkQueue延迟执行任务



**执⾏ execute()⽅法和 submit()⽅法的区别是什么呢？**

* execute() ⽅法⽤于提交不需要返回值的任务
* submit() ⽅法⽤于提交需要返回值的任务， 线程池会返回⼀个 Future 类型的对象 。



**ThreadPoolExecutor分析**





## JMM





## AQS



**什么是QAS**

AbstractQueuedSynchronizer，用来构建**锁**和**同步器**的框架，抽象的队列式的同步器。AQS定义了一套多线程访问共享资源的同步器框架，许多同步类实现都依赖于它，如常用的ReentrantLock/Semaphore/CountDownLatch。

[参考](https://www.cnblogs.com/waterystone/p/4920797.html)

维护了一个volatile int state（代表共享资源）和一个FIFO线程等待队列（多线程争用资源被阻塞时会进入此队列）state相关的三个方法：getState，setState，compareAndSetState。

自定义同步器在实现时只需要实现共享资源state的获取与释放方式即可，至于具体线程等待队列的维护（如获取资源失败入队/唤醒出队等），AQS已经在顶层实现好了。

不同的自定义同步器争用共享资源的方式也不同。自定义同步器在实现时只需要实现共享资源 state 的获取与释放方式即可，至于具体线程等待队列的维护(如获取资源失败入队/唤醒出队 等)，AQS 已经在顶层实现好了。



**对于 AQS 原理的理解**

AbstractQueuedSynchronizer，是一个用来构建锁和同步器的框架。比如我们提到的 ReentrantLock ， Semaphore ，ReentrantReadWriteLock ， SynchronousQueue ， FutureTask 等等皆是基于 AQS的

AQS 核⼼思想是，如果被请求的共享资源空闲，则将当前请求资源的线程设置为有效的⼯作线程，并且将共享资源设置为锁定状态。如果被请求的共享资源被占⽤，那么就需要⼀套线程阻塞等待以及被唤醒时锁分配的机制，这个机制 AQS 是⽤ CLH 队列锁实现的，即将暂时获取不到锁的线程加⼊到队列中。



**AQS 对资源的共享⽅式**

独占，如 ReentrantLock	

共享，如、 Semaphore 、 CountDownLatch 、 CyclicBarrier 、 ReadWriteLock。



**AQS 组件**

Semaphore (信号量)，用来限制某一段代码的并发数，

CountDownLatch (倒计时器)，保证n个线程都执行完之后再执行

CyclicBarrier (循环栅栏):它要做的事情是，让一组线程到达一个屏障(也可以叫同步点)时被阻塞， 直到最后一个线程到达屏障时，屏障才会开⻔





**常见并发工具类**

CountDownLatch

CrclicBarrier

Semaphore

Exchanger



**CountDownLatch与CrclicBarrier的区别**

* CountDownLatch和CyclicBarrier都能够实现线程之间的等待，只不过它们侧重点不同：
  CountDownLatch一般用于某个线程A等待若干个其他线程执行完任务之后，它才执行；
  而CyclicBarrier一般用于一组线程互相等待至某个状态，然后这一组线程再同时执行；
* CountDownLatch是不能够重用的，而CyclicBarrier是可以重用的。CountDownLatch的计数器只能使用一次。而CyclicBarrier的计数器可以使用reset() 方法重置。所以CyclicBarrier能处理更为复杂的业务场景，比如如果计算发生错误，可以重置计数器，并让线程们重新执行一次。
* CyclicBarrier还提供其他有用的方法，比如getNumberWaiting方法可以获得CyclicBarrier阻塞的线程数量。isBroken方法用来知道阻塞的线程是否被中断。如果被中断返回true，否则返回false。
* Semaphore其实和锁有点类似，它一般用于控制对某组资源的访问权限。



```Java
public class CountDownLatchTest {
    CountDownLatch downLatch = new CountDownLatch(3);

    public void test() throws InterruptedException {
        Runnable task = () -> {
            System.out.println("1");
            downLatch.countDown();
            System.out.println(2);
        };
        new Thread(task).start();
        new Thread(task).start();
        new Thread(task).start();
        downLatch.await();
        System.out.println("123");
    }

    public static void main(String[] args) throws InterruptedException {
        new CountDownLatchTest().test();
    }
}

public class CyclicBarrierTest {
    CyclicBarrier barrier = new CyclicBarrier(3);

    Runnable task = ()->{
        System.out.println("1");
        try {
            barrier.await();
        } catch (InterruptedException | BrokenBarrierException e) {
            e.printStackTrace();
        }
        System.out.println(2);
    };

    public void test(){
        new Thread(task).start();
        new Thread(task).start();
        new Thread(task).start();
    }
    public static void main(String[] args) {
        new CyclicBarrierTest().test();
    }
}

public class SemaphoreTest {
    Semaphore semaphore = new Semaphore(5);

    public void test(){
        Runnable task = ()->{
            try {
                semaphore.acquire();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("HELLO");
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            semaphore.release();
        };
        new Thread(task).start();
        new Thread(task).start();
        new Thread(task).start();
        new Thread(task).start();
        new Thread(task).start();
        new Thread(task).start();
        new Thread(task).start();
    }

    public static void main(String[] args) {
        new SemaphoreTest().test();
    }
}
```





**什么是自旋**

很多synchronized中都是简单的代码，执行非常快，不妨不让线程进去阻塞状态，而是在synchronized外循环，循环多次还没获得锁再进行阻塞。



**什么是CAS**

比较交换。是一个乐观锁。

CAS包含了三个操作树：内存位置(V),期望值(A),新值(B).

CAS通过无限循环获得数据，当内存地址和期望值相同时，才会将值替换成新值



**CAS的问题**

* ABA问题
* CAS值保证一个变量的原子性操作，代码块就不行了
* 增加了CPU占用率



## Synchronized



**对于 synchronized 关键字的了解** 

synchronized关键字用来解决线程间的同步问题，synchronized修饰的方法和代码块在同一时间只能被一个线程执行。

synchronized关键字可以用来修饰实例方法，静态方法和代码块。修饰实例方法时，使用的锁对象时this，修饰静态方法时，使用的时当前类的Class对象，修饰代码块时自己指定锁对象。

 尽量不要使⽤ synchronized(String a) 因为 JVM 中，字符串常量池具有缓存功能， 两个String的值一致时指向的地址是一致的，其实两个线程锁的是同一个对象。



**怎么使用 synchronized 关键字**

**修饰实例方法**：作用于当前对象实例加锁，进入同步代码前要获得 当前对象实例的锁

**修饰静态方法**：也就是给当前类加锁，会作用于类的所有对象实例。进入同步代码前要获得 当 前 class 的锁。

如果一个线程 A 调用一个实例对象的 非静态 synchronized 方法，而线程 B 需要调用这个实例对象所属类的静态 synchronized 方法， 是允许的，不会发生互斥现象，因为访问静态 **synchronized** 方法占用的锁是当前类的锁，而访 问非静态 **synchronized** 方法占用的锁是当前实例对象锁。

**修饰代码块**：指定加锁对象，对给定对象/类加锁。 synchronized(this|object) 表示进入同步代码 库前要获得给定对象的锁。 synchronized(􏰄.class) 表示进入同步代码前要获得 当前 **class** 的锁

总结：给Class加锁和给对象加锁



**Sybchornized关键字底层原理**

JVM 层面

1. *同步代码块*

```
synchronized 同步语句块的实现使⽤的是 monitorenter 和 monitorexit 指令，其中
monitorenter 指令指向同步代码块的开始位置， monitorexit 指令则指明同步代码块的结束位
置。
当执⾏ monitorenter 指令时，线程试图获取锁也就是获取 对象监视器 monitor 的持有权。（每个对
象中都内置了⼀个 ObjectMonitor 对象。）如果锁的计数器为 0 则表示锁可以被获取，获取
后将锁计数器设为 1 也就是加 1。
在执⾏ monitorexit 指令后，将锁计数器设为 0，表明锁被释放。如果获取对象锁失败，那当前
线程就要阻塞等待，直到锁被另外⼀个线程释放为⽌。
```

2. 同步方法

```
JVM 通过该
ACC_SYNCHRONIZED 访问标志来辨别⼀个⽅法是否声明为同步⽅法，从⽽执⾏相应的同步调
⽤。
```

不过两者的本质都是对对象监视器 monitor 的获取



**为什么Java 早期版本中， synchronized 属于 重量级锁，效率低下**

因为监视器锁(monitor)是依赖于底层的操作系统的 Mutex Lock 来实现的，Java 的线程是映 射到操作系统的原生线程之上的。如果要挂起或者唤醒一个线程，都需要操作系统帮忙完成，而 操作系统实现线程之间的切换时需要从用户态转换到内核态，这个状态之间的转换需要相对比⻓的时间，时间成本相对高



**Synchronized实现的底层原理**

 monitorenter 和 monitorexit 指令 

 ACC_SYNCHRONIZED 标 



**Synchronized和Lock区别**

sunchronized发生异常会释放锁 



**synchronized 关键字和 volatile 关键字的区别**

* volatile 关键字只能用于变量而 synchronized 关键字可以修饰方法以及代码块
* volatile 关键字能保证数据的可⻅性，但不能保证数据的原子性。 synchronized 关键字两者都能保证
* volatile 关键字主要用于解决变量在多个线程之间的可⻅性，而 synchronized 关键字解决 的是多个线程之间访问资源的同步性



**JDK1.6后synchronized的优化**

JDK1.6 对锁的实现引入了大量的优化，如自旋锁、适应性自旋锁、锁消除、锁粗化、偏向锁、轻量级锁等技术来减少锁操作的开销。



**Synchronized锁升级的过程**

[参考](https://www.cnblogs.com/aligege/p/13897342.html)

最开始是无锁状态，有线程使用时变成偏向锁，有线程争强时变成轻量锁，竞争频繁变成重量锁

无状态锁：最开始是无锁状态

偏向锁：存储ThreadID

轻量级锁:CAS+自旋一段时间

重量级锁：完全阻塞

锁不降级



 **构造方法可以使用 synchronized 关键字修饰么**

不能。

构造方法本身就属于线程安全的。



**使用双重校验锁实现线程安全的单例模式**

```Java
 
public class Singleton {
    private volatile static Singleton uniqueInstance;
    private Singleton() {}
		public static Singleton getUniqueInstance() { 
      //先判断对象是否已经实例过，没有实例化过才进入加锁代码
			if (uniqueInstance == null) { //类对象加锁
            synchronized (Singleton.class) {
                if (uniqueInstance == null) {
                    uniqueInstance = new Singleton();
                }
			} }
        return uniqueInstance;
    }
}
```





## Lock



**什么是ReadWriteLock**

读写锁，实现了读写分离，读锁是共享的，写锁是独占的



**锁分离技术**



**synchronized和ReentrantLock的区别**

* 一个是关键字，一个是类(更加灵活)
* ReentrantLock可以设置获得锁的时间，避免死锁
* ReentrantLock可以或的所得各种信息
* 底层调用？



**ReentrantLock怎么实现可重入的**



**公平锁和非公平锁**

按照线程访问的顺序来获得锁对象



## volatile



轻量级的同步机制，主要的作用是1. 保证变量的可见性，2. 禁止指令重排序

保证可见性：一个线程修改了变量，其它线程可以立马感知到。volatile变量修改时会立马刷新到主内存中，每次都要从内存中重新读取

指令重排序：由于指令重排序的优化，代码的顺序和实际执行的顺序可能会不一致

**缓存一致性协议：**写数据时如果发现其他线程也有这份数据的副本，会发送信号使缓存失效，需要重新从主内存中读取



**创建volatile数组**

只保证了引用的可见性



**volatile能使一个非原子操作变成一个原子操作吗**



**Volatile、CAS**



**volatile 变量和 atomic 变量有什么不同**



wait，join，yeid，notify





定时线程的使用?

什么是竞争条件

如何停止一个线程

什么是自旋





## CAS

**什么使CAS**





## CountDownLatch



任务分为 N 个子线程去执行，state 也初始化为 N(注意 N 要与线 程个数一致)。这 N 个子线程是并行执行的，每个子线程执行完后 countDown() 一次，state 会 CAS(Compare and Swap)减 1。等到所有子线程都执行完后(即 state=0)，会 unpark()主调用线 程，然后主调用线程就会从 await() 函数返回，继续后余动作。



**用过 CountDownLatch 么?什么场景下用的**

CountDownLatch 的作用就是 允许 count 个线程阻塞在一个地方，直至所有线程的任务都执行完毕。多线程处理多个文件(校验)，可以替换成CompletableFuture



## **ThreadLocal **



 **ThreadLocal 了解么** 

给每个线程一个值，避免线程安全问题

 每⼀个线程都有自己的专属本地变量。主要是将线程和线程的私有变成做一个映射，各个线程之间互不影响。高并发的情况下可以实现无状态调用。特别适合各个线程依赖不同变量完成操作的情况。就是空间换时间，各自用各自的变量，自然就不用处理线程安全的问题。

 ThrealLocal 类中可以通过 Thread.currentThread() 获取到当前线程对象后，直接通过 getMap(Thread t) 可以访问到该线程的 ThreadLocalMap 对象。 



 **ThreadLocal 原理** 

Thread 类中有一个threadLocals和inheritableThreadLocals，他们都是它们都是 ThreadLocalMap类型的，默认情况下这两个变量都
是 null，只有当前线程调⽤ ThreadLocal 类的 set 或 get ⽅法时才创建它们，实际上调⽤这两个⽅法的时候，我们调⽤的是 ThreadLocalMap 类对应的 get() 、 set() ⽅法。

 最终的变量是放在了当前线程的 ThreadLocalMap 中 

当前对象调用set方法，实际是将值存在了ThreadLocalMap中，key是当前的Thread对象。



 **ThreadLocal 内存泄露问题**

ThreadLocal.ThreadLocalMap.Entry中的key是弱引用的，也即是当某个ThreadLocal对象不存在强引用时，就会被GC回收，但是value是基于强引用的，所以当key被回收，但是value还存在其他强引用时，就会出现内存的泄露情况，在最新的ThreadLocal中已经做出了修改，即在调用set、get、remove方法时，会清除key为null的Entry，但是如果不调用这些方法，仍然还是会出现内存泄漏 ：），所以要养成用完ThreadLocal对象之后及时remove的习惯。



## **原子类**

基本类型：AtomicInteger，AtomicLong，AtomicBoolean

数组类型：AtomicIntegerArray，AtomicLongArray，AtomicReferenceArray

引用类型：AtomicReference，AtomicStampedReference，AtomicMarkableReference

AtomicStampedReference可以解决ABA问题

对象的属性修改类型：AtomicIntegerFieldUpdater，AtomicLongFieldUpdater，AtomicReferenceFieldUpdater



## 使用场景

**SynchronizationMap与ConcurrentHashMap的区别**



**控制线程执行顺序**

join

condition



多线程下载



# JVM



**什么是JVM，JVM都包括什么**

JVM是Java虚拟机，用来运行Java程序。JVM属于JRE的一部分，



**JVM加载class文件的原理**

类的加载是将class文件中的数据读取到内存，然后进行连接，验证，准备和解析。类的装载有类加载起(ClassLoad)和它的子类完成。

验证：  确保Class文件的字节流中包含的信息符合当前虚拟机的要求，并且不会危害虚拟机自身的安全。

准备：分配内存，并将其赋默认值

解析：将常量池中的符号引用变为直接引用(内存地址)

初始化：执行xxx方法，设置初始值

[参考](https://blog.csdn.net/zhaocuit/article/details/93038538)



**内存划分**  

*线程共有的*：

**堆**

用来存放实例，几乎所有的对象实例都存在堆里，堆时JVM内存管理的核心区域，

JVM中堆是最大的一块内存空间，所有线程共享的一块区域，在虚拟机启动的时候创建。堆还可以细分，详情看GC部分。随着JIT编译期的发展与逃逸分析技术逐渐 成熟，栈上分配、标量替换优化技术将会导致一些微妙的变化，所有的对象都分配到堆上也渐渐 变得不那么“绝对”了。

从jdk 1.7开始已经默认开启逃逸分析，如果某些方法中的对象引用没有被 返回或者未被外面使用(也就是未逃逸出去)，那么对象可以直接在栈上分配内存。

**方法区**

存储已被虚拟机加载的类信息，常量，静态变量等。方法区也称为永久代。常量池是方法区的一部分

jdk1.8取消了方法区，用元空间代替。

*线程私有的*

**程序计数器**

记录着当前线程正在执行的字节码的地址

* 通过修改程序计数器实现流程控制

* 线程切换后根据程序计数器能恢复到正确的执行位置。

**虚拟机栈**

先进后出，模仿方法的调用，保存着栈帧，描述方法执行的内存模型。每一次函数调用都会有一个对应的栈帧被压入 Java 栈，每一个函数调用结束后，都会有一个栈帧被弹出。

*栈帧*：存放着局部变量表、操作数（operand）栈、动态链接、方法正常退出或者异常退出的定义等 。 

*局部变量*：表存放着编译期可知的各种数据类和对象的引用

*操作数栈*：

*动态连接*：

Java虚拟机栈可能会出现两种错误：StackOverFlowError 和 OutOfMemoryError

***StackOverFlowError*** : 若 Java 虚拟机栈的内存大小不允许动态扩展，那么当线程请求栈 的深度超过当前 Java 虚拟机栈的最大深度的时候，就抛出 StackOverFlowError 错误。

***OutOfMemoryError*** : 若 Java 虚拟机堆中没有空闲内存，并且垃圾回收器也无法提供更多 内存的话。就会抛出 OutOfMemoryError 错误。

**本地方法栈**

与虚拟机栈相似，但是本地方法栈执行的是native方法。在hotspot虚拟机中本地方法栈和Java虚拟机栈合而为一。



堆和方法区是所有线程共享的资源，其中堆是进程中最大的一块内存，主要用于存放新创建的对 象 (所有对象都在这里分配内存)，方法区主要用于存放已被加载的类信息、常量、静态变量、即 时编译器编译后的代码等数据

PC：程序计数器。字节码解释器通过改变程序计数器来依次读取指令，从而实现代码的流程控制；在多线程的情况下，程序计数器用于记录当前线程执行的位置，从而当线程被切换回来的时 候能够知道该线程上次运行到哪儿了。如果执行的是 native 方法，那么程序计数器记录的是 undefined 地址，只有执行 的是 Java 代码时程序计数器记录的才是下一条指令的地址。

Java8取消了方法区，而将常量放在了元空间(直接内存)



**什么时候会站溢出**



**什么是对象头**

在JVM中需要大量存储对象，存储时为了实现一些额外的功能，需要在对象中添加一些标记字段用于增强对象功能，这些标记字段组成了对象头。

* Mark Word 主要存放对象的hash，GC的年龄
* Klass Word 存储对象的类型指针，JVM通过这个指针确定对象是哪个类的实例
* array length 数组对象还会存储数组的长度



**直接内存是什么**

直接内存不属于运行时数据区，不是JVM规范规定的区域。但这个区域频繁使用，会造成内存溢出。

NIO基于通道和缓冲区，堆内保存DirectByteBuffer作为内存的引用，使用native函数直接分配堆外的内存，避免了native方法与Java之间来回复制数据。



**Class的文件结构**



**类加载过程**



**创建对象的过程**

1. 类加载检查：检查是否已经被加载过(到方法区中找)，没有的话要执行类加载操作
2. 分配内存：开辟堆内存空间
3. 初始化零值：就是设置默认值
4. 设置对象头：比如这个对象的Hash,GC分代的年龄，是哪个类的实例
5. 执行init方法：父类变量的初始化、语句块，构造器，子类的变量初始化，代码块，构造器

* 父类>子类，静态>非静态，代码块>构造方法



**什么是双亲委派模型**



**为什么需要双亲委派模型**

* 防止类重复加载



**怎么破坏双亲委派机制**

1. 自定义类加载器，重写findClass方法



**类加载器的作用**

* 将字节码文件加载到JVM中
* 创建Class对象



**为什么使用自定义类加载器**





**字符串常量池到底存在于内存空间的哪里**  

* jdk 6.0 字符串常量池在方法区，方法区的具体体现可以看做是堆中的永久区。

* jdk 7.0 java 虚拟机规范中不再声明方法区，字符串常量池存放在堆空间中

* jdk 8.0 java 虚拟机规范中又声明了元空间，字符串常量池存放在元空间中。



 **Java 中的编译期常量是什么？**

编译时就能够知道变量的值并且值不会再改变的变量，会直接替换成值。这种就是编译期常量(即使那种final修饰的并且直接赋值的)



**运行时常量池**

运行时常量池是方法区的一部分。Class 文件中除了有类的版本、字段、方法、接口等描述信息
外，还有常量池表(用于存放编译期生成的各种字面量和符号引用



**什么是类加载器，有哪些作用**

用来将一个class文件加载进内存， 并对数据进行检验、转换解析和初始化，最终创建一个Class对象。



**类加载的过程**

* 加载
* 链接
* 初始化



**类加载器的种类**

* 启动类加载器(Bootstrap ClassLoader): 最顶级的加载器， 负责加载jvm的核心类库，比如`java.lang.*`等，从系统属性中的`sun.boot.class.path`所指定的目录中加载类库 
* 扩展类加载器(Extension ClassLoader)：从java.ext.dirs系统属性所指定的目录中加载类库，或者从JDK的安装目录的jre/lib/ext子目录（扩展目录）下加载类库，如果把用户的jar文件放在这个目录下，也会自动由扩展类加载器加载。
* 应用程序类加载器(Application ClassLoader)：从环境变量classpath或者系统属性java.class.path所指定的目录中加载类。
* 自定义加载器(User ClassLoader)



**什么是双亲委派模型**

根据双亲委派模式，在加载类文件的时候，子类加载器首先将加载请求委托给它的父加载器，父加载器会检测自己是否已经加载过类，如果已经加载则加载过程结束，如果没有加载的话则请求继续向上传递直Bootstrap ClassLoader。如果请求向上委托过程中，如果始终没有检测到该类已经加载，则Bootstrap ClassLoader开始尝试从其对应路劲中加载该类文件，如果失败则由子类加载器继续尝试加载，直至发起加载请求的子加载器为止。



**为什么要使用双亲委派模型**

 免类的重复加载，这样就可以保证任何的类加载器最终得到的都是同样一个Object对象。 



**双亲委派模型缺陷**



**怎么打破双亲委派模型**

自定义类加载器，重写loadClass方法自己指定使用哪个类加载器来加载。



**SPI为什么要打破双亲委派机制**



**自定义类加载器的用途**

* 依赖冲突，各自加载各自的依赖
* 加密



**32位JVM和64位JVM最大的内存**

32位：2^32(4GB)

64位：2^64，很大，1000G都可以



**获得JVM位数**

sun.arch.data.model或者os.arch



**获得Java程序的内存使用信息**

Runtime类的freememory()等方法



**CPU飙升怎么排查**



# GC



GC主要是对堆内存进行GC。在JDK1.8之前，堆通常被分成新生代，老年带和永久代，JDK1.8取消了永久代，用元空间代替。新生代有分为 Eden 区、From Survivor0("From") 区、To Survivor1("To") 。



**为什么用元空间替换永久代**

整个永久代有一个 JVM 本身设置固定大小上限，无法进行调整，而元空间使用的是直接内 存，受本机可用内存的限制，虽然元空间仍旧可能溢出，但是比原来出现的几率会更小。



**都有哪些垃圾回收算法，都有哪些弊端**

* 标记清除： ⾸先标记出所有不需要回收的对象，在标记完成后统⼀回收掉所 有没有被标记的对象。 造成了内存碎片
* 复制算法： 以将内存分为大小相同的两块，每次使⽤其中 的⼀块。当这⼀块的内存使⽤完后，就将还存活的对象复制到另⼀块去，然后再把使⽤的空间⼀次清理掉。浪费了一半内存。
* 标记整理：标记过程仍然与“标记-清除”算法⼀样，但后续步骤不是直接对可回收对象回收，⽽是让所有存活的对象向⼀端移动，然后直接清理掉端边界以外的内存。移动增加了开销。
* 分代收集：般将 java 堆分为新⽣代和⽼年代，每个代使用不同的算法。



**晋升的流程**

*Minor GC*

触发时机：当 eden 区没有足够空间进行分配时

大部分情况，对象都会首先在 Eden 区域分配，在一次新生代垃圾回收后，如果对象还存活，则 会进入 s0 或者 s1，并且对象的年龄还会加 1(Eden 区->Survivor 区后对象的初始年龄变为 1)， 当它的年龄增加到一定程度(默认为 15 岁)，就会被晋升到老年代中。经过这次 GC 后，Eden 区和"From"区已经被清空。这个时候，"From"和"To"会交换他们的⻆色。Minor GC 会一直重复这样的过程，直到“To”区被填 满，"To"区被填满之后，会将所有对象移动到老年代中。

* 对象优先分配到Eden，大对象如果进行一次Minor GC后还存不下，直接进入老年代

* 动态对象年龄判定：“Hotspot 遍历所有对象时，按照年龄从小到大对其所占用的大小进行累 积，当累积的某个年龄大小超过了 survivor 区的一半时，取这个年龄和 MaxTenuringThreshold 中更小的一个值，作为新的晋升年龄阈值”

*Full GC*



**如何判断一个对象应该被回收**  

1. 引用计数法：给对象中添加一个引用计数器，每当有一个地方引用它，计数器就加1;当引用失效，计数器就
   减1;任何时候计数器为0的对象就是不可能再被使用的。
2. 可达性分析算法：这个算法的基本思想就是通过一系列的称为 “GC Roots” 的对象作为起点，从这些节点开始向下 搜索，节点所走过的路径称为引用链，当一个对象到 GC Roots 没有任何引用链相连的话，则证 明此对象是不可用的。



**什么时候回收Class**

* 该类所有的实例都已经被回收，也就是 Java 堆中不存在该类的任何实例。 
* 加载该类的 ClassLoader 已经被回收。 
* 该类对应的 java.lang.Class 对象没有在任何地⽅被引⽤，⽆法在任何地⽅通过反射访问该类 的⽅法。 



**哪些对象可以作为GC Root**

* 栈中引用的对象
* 常量/静态属性中引用的对象



**老年代为什么使用标记整理和标记清除，新生代为什么要使用复制算法**

触发Full GC的频率要低，里面保存的对象被GC的可能性小，大对象也保存在老年代中，老年代还需要自己兜底内存，标记清楚和标记整理不会浪费内存，所以使用这两个方法。

新生代中的对象大部分都会被GC，所以使用复制算法效率高，而且空间也并不是1:1等分而是8:1:1,只浪费了10%内存。



**一个对象是如何从新生代到老年代的**





**都有哪些垃圾收集器**

Serial 收集器，串行收集器是最古老，最稳定以及效率高的收集器，可能会产生较长的停顿， 只使用一个线程去回收。
ParNew 收集器，ParNew 收集器其实就是 Serial 收集器的多线程版本。
Parallel 收集器，Parallel Scavenge 收集器类似 ParNew 收集器，Parallel 收集器更关注系统的吞吐量。
Parallel Old 收集器，Parallel Old 是 Parallel Scavenge 收集器的老年代版本，使用多线程和“标记－整理”算法
CMS 收集器，CMS（Concurrent Mark Sweep）收集器是一种以获取最短回收停顿时间为目标的收集器。

G1 收集器，G1 (Garbage-First)是一款面向服务器的垃圾收集器,主要针对配备多颗处理器及大容量内存的机器. 以极高概率满足 GC 停顿时间要求的同时,还具备高吞吐量性能特征



**堆内存中，对象的分配策略**

* 一般都会先进入eden
* 大对象直接进入老年代(eden放不下)
* 长期存活的对象进入老年代



**简单的介绍一下强引用,软引用,弱引用,虚引用**

强引用引用：大部分引用实际上都是强引用，垃圾回收器绝不会回收它

软引用：可有可无，如果内存空间足够，垃圾回收器 就不会回收它，如果内存空间不足了，就会回收这些对象的内存。

弱引用：可有可无，但是被辣鸡回收器发现会被回收

虚引用：形同虚设，主要用来跟踪对象被垃圾回收的活动。当垃圾回收器准备回收一个对象时，如果发现它还有虚引用，就会在回收对象的内存之前， 把这个虚引用加入到与之关联的引用队列中。程序可以通过判断引用队列中是 否已经加入了虚引 用，来了解被引用的对象是否将要被垃圾回收。程序如果发现某个虚引用已经被加入到引用队 列，那么就可以在所引用的对象的内存被回收之前采取必要的行动。



**怎么回收无用类**

方法区主要回收类信息

要判定一个类是否是“无用的类”的条件则相对苛刻许 多。类需要同时满足下面 3 个条件才能算是 **“**无用的类**”** ：

* 该类所有的实例都已经被回收，也就是 Java 堆中不存在该类的任何实例。
* 加载该类的 ClassLoader 已经被回收
* 该类对应的 java.lang.Class 对象没有在任何地方被引用，无法在任何地方通过反射访问该类 的方法。



**内存泄露**

理论上因为有GC，不会导致内存泄露，然而实际开发中，会存在一些无用但是可达的对象导致不能被GC，



**怎么定位内存泄露问题**



**如果对象的引用被置为null，GC是否会立即释放对象占用的空间**



**串行收集器和吞吐量收集器的区别是什么**

吞吐量收集器采用并行的新生代垃圾收集器，用于中大规模应用

串行收集器对于大多数小型(100M内存左右)足够了



**串行收集器和吞吐量收集器的区别**







# Java8新特性



## **Stream API**

，充分利用现代多核 CPU  



**数组获得Stream**

Array.stream(array[])

Stream.of(T...)



## **Time API**  

接口  



**老API线程不安全**

原因：在多线程环境下，当多个线程同时使用相同的SimpleDateFormat对象（如static修饰）的话，如调用format方法时，多个线程会同时调用calender.setTime方法，导致time被别的线程修改，因此线程是不安全的。



**获得两个日期的间隔**

`ChronoUnit.DAYS.between(date1, date2)`



**获得时间戳**



 **JDK 1.7 中的新特性**

* try-with-resource ，不需要手动关闭流
* switch，允许String
* 类型推断，<>推断
* catch可以捕获多个异常



**JDK1.8的新特性**

* lambda表达式
* StreamAPI
* Date与TimeAPI
* 接口中可以有静态方法和默认方法

































make PREFIX=~/2022/redis/ install



## CompletableFuture



## 为什么不建议使用Arrays.asList、ArrayList的subList





设计模式

66道































数据结构与算法

**双向链表和双向循环链表**



# other



**JDBC的使用步骤**

加载驱动

获得数据库连接

执行SQL语句

获得结果集

关闭连接



**数据库连接池技术**



没有临时变量的情况下怎么交换两个值



# 算法



**折半查找**



**冒泡排序**



**打印出一个字符传的所有排列**



**获得数组中重复的元素**



**不添加临时变量交换两个变量的值**





# 设计模式

55



**什么是设计模式，为什么要使用设计模式**

代码涉及经验的总结，为了能重用代码，提高代码可读性



**涉及模式的分类**



**单例模式**

双重校验实现单例模式



**什么是里氏替换原则**



**什么情况下会违反迪米特法则**



**什么时候用适配器模式**



**适配器模式和装饰器模式的区别**



**适配器模式和代理模式的区别**



**什么是模板方法模式**



访问者模式



组合模式



**继承和组合有什么不同**





**聚合和关联有什么区别**



**开闭原则**

对扩展开发，对修改关闭，增加新功能时只需要实现接口。



**抽象工厂模式和原型模式的区别**



**享元模式**



# 网络



**TCP/IP的五层协议**

物理层，数据链路层，网络层，传输层，应用层



## **http**

设计 HTTP 最初的目的是为 了提供一种发布和接收 HTML ⻚面的方法



**各种协议与HTTP协议之间的关系**



**HTTP的长连接和短连接**

实际是TCP的长连接和短连接

在HTTP/1.0中默认使用短连接。也就是说，客户端和服务器每进行一次HTTP操作，就建立一次 连接，任务结束就中断连接。当客户端浏览器访问的某个HTML或其他类型的Web⻚中包含有其 他的Web资源(如JavaScript文件、图像文件、CSS文件等)，每遇到这样一个Web资源，浏览 器就会重新建立一个HTTP会话。而从HTTP/1.1起，默认使用⻓连接，用以保持连接特性。使用⻓连接的HTTP协议，会在响应头 加入这行代码:`Connection:keep-alive`



ftp



SMPT



**TCP与UDP的区别**

两者都是传输层的协议，TCP提供面向连接的，可靠的数据传输 服务。UDP提供无连接的，尽最大努力的数据传输服 务(不保证数据传输的可靠性)。



**TCP的三次握手和四次挥手**

三次握手是为了**双方能确认对方可以进行收发**。

第一次握手，service，确认了client可以发送数据，自己可以接收数据

第二次握手，client确认了自己的收发没问题

第三次握手，service确认自己的发送和对方的接收没问题

客户端现发送SYN信号，服务端使用SYN-ACK应答，客户端在恢复ACK建立联系。

断开一个连接则需要"四次挥手"

**确保双方都没有数据要传输了**，即双方都要发送关闭连接的请求，然后等对方同意，就需要四次挥手了。

任何一方都可以在数据传送结束后发出连接释放的通知，待对方确认后进入半关闭状态。当另一 方也没有数据再发送的时候，则发出连接释放通知，对方确认后就完全关闭了TCP连接。

比如A想要关闭连接，B收到后确认，但是它还有东西要传输，A就会处于半连接状态，等B也发送关闭请求时，A发送确认然后关闭连接。



**在浏览器中输入url地址 到 显示主⻚的过程**

1. DNS域名解析获得IP
2. 根据HTTP协议生成请求报文
3. 建立TCP连接进行报文传输
4. 路由选择



**套接字编程实现回显**



**什么是跨域**

从一个域名的网页去访问另一个域名的网页

协议+IP+port

解决方案：

1. Nginx
2. CORS跨域资源共享，需要在服务端实现，指定允许哪些域名访问哪些资源