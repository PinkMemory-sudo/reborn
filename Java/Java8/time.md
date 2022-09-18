

获得当前时间戳，格式化，计算，与字符串的转换，自定义时间

Date的线程不安全？

* 表示时间的四个类
* 获得时间
* 修改时间
* 计算时间
* 时间转换
* ZoneId



* 时间的调整和计算



Instant加上时区就变成了ZoneDateTime



***java.time***



# Instant



表示一个时间点(date+time+zone)，可以把它看成时间戳

常用功能：

* 获得时间戳
* Date,LocalDateTime,Instant与字符串的相互转换

时间线上的一个时间点。java8给Date和Calendar添加了toInstant方法来获得Instant，form(Instant)来获得Date，所以Instant可以看成是时间转换的中间量。



**静态方法**

| 方法                                  | 描述                        |
| ------------------------------------- | --------------------------- |
| Instant.now()                         | 获得当前的时间点            |
| ofEpochSecond(long epochSecond)       | 根据秒级时间戳获得Instant   |
| Instant ofEpochMilli(long epochMilli) | 通过毫秒极时间戳获得Instant |
| from(TemporalAccessor temporal)       | 根据提供的时间获得Instant   |



非静态方法

| 方法                              | 描述                                       |
| --------------------------------- | ------------------------------------------ |
| getEpochSecond()                  | 获得该时间点的秒级时间戳                   |
| getNano()                         | 时间戳的纳秒部分                           |
| toEpochMilli                      | 获得毫秒几时间戳                           |
| long getLong(TemporalField field) | 获得指定的时间戳部分(秒，毫秒，微妙，纳秒) |
| atZone(ZoneId zone)               | 获得一个ZonedDateTime                      |



**获得Instant的方式**

* 根据Instant的静态方法，获得当前的，指定ZoneDataTime的，指定时间戳的



# LocalDate



**静态方法**

从现在，字符串，手动年月如获得日期

| 方法                                                  | 描述                        |
| ----------------------------------------------------- | --------------------------- |
| new                                                   | 获得当前日期,可以指定ZoneId |
| of(int year, int month, int dayOfMonth)               | 手动设置年月日              |
| parse(CharSequence text, DateTimeFormatter formatter) | 通过字符串转换              |





***TemporalField***

实现子类：ChronoField枚举类，来表示一段时间内的某一时刻。

| 方法             | 描述 |
| ---------------- | ---- |
| SECOND_OF_MINUTE | 秒   |
| MILLI_OF_SECOND  | 毫秒 |
| MICRO_OF_SECOND  | 微秒 |
| NANO_OF_SECOND   | 纳秒 |



## Duration

表示秒或者纳秒时间间隔，适合处理较短的时间，有很高的精确性



## Period

表示一段时间，可用来对时间进行操作(添加/删除一段时间后的时间)

构造方法都是私有的

| 方法                                | 描述 |
| ----------------------------------- | ---- |
| ofYears                             |      |
| ofMonths                            |      |
| ofWeeks                             |      |
| ofDays                              |      |
| of(int years, int months, int days) |      |



Month枚举类，包含12个月的常量，

创建日期时，可以用Month枚举创建日期，记不住英文可是使用of方法。

Month.of(1)相当于Month.JANUARY，这样我们可以讲传进来的数字转换成对应的枚举。



## 可以表示具体时间的类







### LocalTime

不可变的，不提供公共构造函数



### LocalDateTime

不可变的，不提供公共构造函数



### ZonedDateTime

包含时区的DateTime，不提供公共构造函数



ZoneId

| 方法                               | 描述             |
| ---------------------------------- | ---------------- |
| systemDefault                      | 获得系统默认时区 |
| Set\<String> getAvailableZoneIds() | 获得所有时区     |
| of(String zoneId)                  | 获得指定时区     |
|                                    |                  |



## 方法



### now方法

Xxx.now()来根据当前时间返回一个Xxx实例(即现在是多少年月日，时分秒)。

* Year.now()

* YearMonth.now()
* MonthDay.now()



### 修改现有实例来获得新的实例

增加或删除

* plusDay，plusWeek，plusMonth，plusYear，时分秒，纳秒
* plus的单独使用：使用场景：需要加上两年三个月八天，可以使用上面的一步一步加，可以传入一段时间***Period.of(1，3，8）***
* minus同理

修改

使用with

1. 修改几号withDayOfMonth，同样的，还有年月日，时分秒，纳秒

2. 传入调节器

***TemporalAdjuster***

函数式接口，封装了许多常见时间点，比如下个月的第一天，上个月的最后一天等，作为with的参数。

在ISO-8601中，不带时区的date



***TemporalQuery***





Instant 获得时间戳

Date与LocalDateTime转换。

TemporalField ChronoField

ZoneId，封装了600个时区，可以通过

Clock

DateTimeFormatter

TemporalAdjusters 日期计算，提供的各种日期时间格式化的静态类







不可变对象



of就是自定义

with就是修改



Date与Loacl的转换

Date是含有时区的，所以能和ZonedDateTime进行转换。

Date从1.8起，可以获得Instant，可以通过Instant获得Date

ZonedDateTime可以获得Instant，Instant的AtZoned()可以获得ZonedDateTime

Date转LoaclDateTime







比较与计算

# 获得时间

* 获得当前时间：now获得当前时间，Instant获得当前时刻(时间戳)
* 获得指定时间：of获得指定时间
* 通过prase，根据字符串获得时间(根据需要传入DateTimeFormatter)





# 调整时间



## plus/minus

添加或减小当前时间plus/minus

***粗准焦螺旋*** (天级及以上)

已经封装好的又添加一天，一周，一月，一年的方法。

plus/minus的重载方法：

`plus(TemporalAmount amount)`添加n年n月n天

TemporalAmount的实现类Period

`Period.of(of(int years, int months, int days))`

***~~细准焦螺旋~~*** (天级以下)

`plus(long amountToAdd, TemporalUnit unit);`

TemporalUnit的实现类ChronoUnit

枚举类，从纳秒到半天到实际到FOREVER



## with

已经封装好的有调整到第几年、月、日(几号/一年中的第几天)

`with(TemporalAdjuster adjuster)`

TemporalAdjuster的实现类TemporalAdjusters，提供了许多静态方法，可以调整到一年/月/日中的第一天/最后一天。此外还有几个方法next获previous与DayOfWeek枚举类搭配，调整到上一周/下一周的周几。此外，还有OrSame，表示如果今天就是周几，就不有减一周或加一周了。





# 时间转换



字符串与Time的转换



Date与Time的转换









TemporalAdjuster调节器

实际就是封装了一些常用日期调节，就是封装了with，可以快速的将当前日期调整到年/月/日的第一天获最后一天，也可以使用

`dayOfWeekInMonth(int ordinal, DayOfWeek dayOfWeek)`表示调整到这个月的第几个星期的周几。

```JAVA
public static TemporalAdjuster firstDayOfMonth() {
    return (temporal) -> temporal.with(DAY_OF_MONTH, 1);
}
```

DayOfWeek

























Temporal

框架极接口，表示时间对象，比如date,time，offset等































字符串转LocalDate/LocalDateTime

通过调用LocalDate/LocalDateTime的静态方法prase



LocalDate/localDateTime转字符串

通过DateTimeFormatter的format方法



根据字符串获得时间戳





字符串->LocalDate(Time)->ZoneDateTime->Instant



已知时间戳，能直接得到什么？

Instant



已知Instant，能直接得到什么？

时间戳，ZoneDateTime





已知DateTime，能直接获得什么？

字符串，ZoneDateTime



已知字符串，能直接获得什么？

DateTime





获得ZoneDateTime

* 通过Instant



LocalDate转LocalDateTime



adjustInto





# Duration



用来获得指定时间段内有多少秒或毫秒

可以用ofDay,ofHour等传入一段时间或者用between传入两个时间点

| 方法                                                    | 描述                     |
| ------------------------------------------------------- | ------------------------ |
| ofDays(long days)                                       | days天一共有多少秒/毫秒  |
| ofHours(long hours)                                     |                          |
| between(Temporal startInclusive, Temporal endExclusive) | 指定起始时间内一共有几秒 |











































**TemporalAccessor**



顶级接口

| 方法                       | 描述                                                 |
| -------------------------- | ---------------------------------------------------- |
|                            |                                                      |
| range(TemporalField field) | 获得当前时刻指定字段的区间，如常用的当前月天数的区间 |
| get(TemporalField field)   | 获得当前时间是几号/几月/周几等指定字段               |
| getLong()                  |                                                      |







# Temporal



日期时间的Temporal是顶层接口,LocalDate,LocalDateTime,LOcalTime,ZoneDateaTime等时间类都实现了该接口。

该接口规定了增加修改减小时间日期的方法

TemporalQuery

TemporalAccessor

TemporalField

ChronoField

TemporalField的实现子类(枚举类)，封装了一些常用的时间字段，如一周的第几天，一月的第几天，一年的第几天，一分钟的第几秒等。









# TemporalAdjuster



用来调节时间，但是LocalDate等内部已经封装了足够的时间调整操作



















**LocalDate,LocalTime,LocalDateTime**



| 方法    | 描述                     |
| ------- | ------------------------ |
| now()   | 获得当前的时间           |
| from()  | 通过时间戳获得           |
| of()    | 手动指定时间             |
| with()  | 修复时间                 |
| withXXX | 修改成周X，这个月的x号等 |
| pulsxxx | 添加年月日时分秒         |
| minus   | 减少                     |



都是Temporal, TemporalAdjuster的实现类





调整日期

LocalDate，LocalDatetime，LocalTime



调整到最后一天

调整到第一天







**时间戳，字符串，时间三者的相互转换**



三者之间的转换需要离不开ZoneDateTime

* Instant加上时区就变成了ZoneDateTime
* ZoneDateTime含有toInstant方法
* 字符串借助prase转成时间
* 时间通过format转成字符串





# 时间调整

LocalDate，LocalDateTime，LocalTime等，自己封装了许多with方法，可以改变年月日时分秒一级星期

还有许多plus和mimus方法

































































