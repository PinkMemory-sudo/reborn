# 基础

* 每条语句一;或者\g或者\G结尾，\g或\G的结果会增加可读性
* 注释：# 单行注释，/**/多行注释



**一条SQL在MySQL中是如何运行的**

![1645250645967](..\..\image\1645250645967.png)

查询缓存：8.0废弃掉了，命中率太低。

解析：对语法和词法i进行分析

优化器：使用什么索引，怎么执行SQL



**登录数据库**

```bash
mysql -h localhost -u root -p
```

**查看MySQL版本**

登录MySQL后使用select version();

或者不登录，直接使用 mysql --version获得mysql -V



# DDL



数据定义语言。用于数据库，表，视图和索引等的创建修改和删除



| 命令                                     | 描述                                    |
| ---------------------------------------- | --------------------------------------- |
| show databases;                          | 查看数据库列表                          |
| use 数据库名                             | 选择数据库                              |
| show tables                              | 查看当前数据库中存在的表                |
| desc 表名                                | 查看表结构,等效与show column from 表名; |
| show crate database/table  数据库名/表名 | 查看建库(表语句)                        |
| RENAME                                   |                                         |
| TRUNCATE                                 |                                         |



## 数据库



**查看数据库**

```mysql
show databases;
```



可以看到四个系统自带的数据库：

| 数据库             | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| user               | 用来保存用户信息，如root用户                                 |
| information_schema | 保存元数据信息。 MySQL服务有多少个数据库，各个数据库有哪些表，各个表中的字段是什么数据类型，各个表中有哪些索引，各个数据库要什么权限才能访问 |
| performace_schema  | 收集数据库服务器性能参数； 提供进程等待的详细信息，包括锁、互斥变量、文件信息 |
| test               | 空的数据库                                                   |

**选择数据库**

```mysql
use database_name;
```

**创建数据库**

```mysql
CREATE DATABASE [if not exists] database_name;
```

**修改数据库**

不太可能需要修改

修改字符集

```mysql
ALTER DATABASE database_name CHARACTER SET utf8;
```

**删除数据库**

```mysql
DROP DATABASE [id exists] database_name;
```



## 数据表



**创建表**

```MySQL
CREATE TABLE `test` (
  `id` int(11) NOT NULL AUTO_INCREMENT COMMENT '字段的注释',
  `status` int(11) NOT NULL DEFAULT '0' COMMENT '状态',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='表创建测试'
```

自己的字符串(表名，字段名等，最好加反引号，防止与数据库的字段冲突)

```mysql
CREATE TABLE table_name(
	列名 类型 [约束] 备注,
    ...
    主键
    外键
    索引
);
```

* 数据类型，默认值，索引，字符集，存储引擎



### 数据类型

**字符串**

| 类型         | 描述       |
| ------------ | ---------- |
| char(n)      | 定长字符串 |
| varchar(n)   | 边长字符串 |
| Text,Blog... |            |

* n表示字符个数不是字节个数

**整型**

| 类型                | 描述                |
| ------------------- | ------------------- |
| TINYINT             | 1 Bytes(-128，127)  |
| INT                 | 4 Bytes(正负21亿)   |
| BIGINT              | 8 Bytes极大的整数值 |
| SMALLINT，MEDIUMINT |                     |

* int(m)里的m是表示SELECT查询结果集中的显示宽度，并不影响实际的取值范围

**浮点型**

一般不用浮点型，而是使用字符串或者扩大n被存成整型或者DECIMAL

| 类型         | 描述                                   |
| ------------ | -------------------------------------- |
| FLOAT        | 4 Bytes                                |
| DOUBLE       | 8 Bytes                                |
| DECIMAL(M,D) | 依赖于M和D的值，M是总位数，D是小数位数 |

**日期时间**

| 类型      | 描述                     |
| --------- | ------------------------ |
| DATE      | YYYY-MM-DD               |
| TIME      | HH:MM:SS                 |
| DATETIME  | YYYY-MM-DD HH:MM:SS      |
| TIMESTAMP | YYYYMMDD HHMMSS          |
| YEAR      | YYYY，只存储年，节约内存 |

* 都映射成Java的什么类型

***DATETIME与TIMESTAMP***

* 范围不同：timestamp2038年结束
* 时区：timestamp不用考虑时区

***获得当前时间***

CURRENT_TIMESTAMP
CURRENT_TIMESTAMP()
NOW()
LOCALTIME
LOCALTIME()
LOCALTIMESTAMP
LOCALTIMESTAMP



**其他**

| 类型 | 描述       |
| ---- | ---------- |
| JSON | 对象或数组 |
|      |            |
|      |            |

* 怎么和Java映射
* 怎么对JSON中的字段进行增删改查



### 完整性约束条件

| 约束条件       | 说明                           |
| -------------- | ------------------------------ |
| PRIMARY KEY    | 唯一非空标记一行               |
| FOREIGN KEY    | 表示该值与另外一张表的主键关联 |
| NOT NULL       | 非空                           |
| UNIQUE         | 唯一(可以有个空值)             |
| AUTO_INCREMENT | 自增(从1开始)                  |
| DEFAULT        | 设置默认值                     |



***修改时自动更新时间***

 

### 索引



### 字符集





## 修改表

**修改表**

可以修改：

* 列名
* 表名
* 类型
* 约束
* 添加列

ALTER TABLE warn ADD COLUMN batch_id varchar(24);

**修改列**

```mysql
ALTER TABLE table_name CHANGE COLUMN 旧列名 [新列名] 类型 [约束];
```

**添加列**

```mysql
ALTER TABLE table_name ADD COLUMN 列名 类型 [约束];
```

**删除列**

```mysql
ALTER TABLE table_name DROP COLUMN 旧列名 [新列名] 类型 [约束];
```

**修改表名**

```mysql
ALTE TABLE table_name RENAME TO new_table_name;
```

**删除表**

```mysql
DROP TABLE [if exists] table_name;
```

**修改表的时候添加约束**

列级约束

```mysql
ALERT TABLE table_name MODIFY COLUNM colunm_name 类型 约束
```

表级约束

```mysql
ALERT TABLE table_name [CONSTRSINT name] ADD PRIMARY KE(id)
```



**修改表的时候删除约束**



```mysql
ALERT TABLE table_name MODIFY COLUNM colunm_name
```



```mysql
ALERT TABLE table_name DROP PRIMARY KEY
```



```mysql
ALERT TABLE table_name DROP INDEX index_name;
```



## 查看表

**查看都有哪些table**

`show tables` 查看当前数据库中都有哪些表

`show table from 数据库名` 查看指定数据库中有哪些表

**查看字段**

```mysql
desc 表名;
```

或者

```mysql
show columns from 表名
```

**查看索引**

```mysql
show index from 表名
```



**复制表结构**

```mysql
CREATE TABLE table_name1 like table_name2
```

**复制表结构+数据**

```mysql
CREATE TABLE table_name1
SELECT * FROM table_name2;
```

* 复制部分表结构/数据，可以加WHERE



# DML



数据操作语言，用于对数据的增删改。



**时间类型的插入和查找**



## 插入



* 支持批量插入

* 支持子查询

```mysql
INSERT INTO table_name (filed1_name,...) VALUES(value1,...),(...);
```

* 插入的值的顺序与指定的列名顺序和个数相同时可以不用指定field
* VALUES后可接多个值，逗号隔开



```mysql
INSERT INTO table_name VALUES(value1,...),(...);
```

插入的值的顺序表中所有列名顺序和个数相同



## 修改



**单表更新**

```mysql
UPDATE table_name
SET column_name1=new_value,column_name2=new _value...
[WHERE ...]
```



级联更新(只支持内联结)

```mysql
UPDATE table_name1,table_name2
SET ...
WHERE 联结条件 AND 筛选条件
```



## 删除



```mysql
DELETE FROM table_name
where ...;
```



**删除整个表**

```mysql
TRUNCATE table table_name
```

***Delete与Trancate的区别***

两者都只是清空表，表结构保留

1. 事务，Delete时DML，可以进行回滚，Trancate是DDL不能回滚
2. 自增的主键，Delete删除后继续自增，Trancate同1开始(trancate会清空空间，delete不会)
3. Trancate只能作用于表



**级联删除**

```mysql
DELETE table_name
FROM table1,table2
WHERE 联结条件
AND 筛选条件
```

* DELETE后加要删除的表，两张表都需要删除时就都写上



# DQL



数据查询语言。

```
select  column(s) from table
where  condition
```



select后都可以加什么？

select可以理解成print



## **where**



用来声明查询条件来限定行；可以通过AND、OR来组合条件。

MySQL的计算次序是AND>OR,可以通过()改变优先级。



**between** 

where price between 100 and 200

闭区间，可以是数值或日期



**空值检查**

is null

is not null

所有运算符与NULL计算结果都为NULL



**in**

()除了用来改变优先级，还可以来封装一组范围，与in连用，表示字段的值在范围内的都能匹配，()内的多个字段值用逗号隔开。

in的作用等同于or但是执行效率要比or快

in ()可以包含其他select语句



**NOT**

not的作用就是否定在它之后的条件

MySQL中的not仅支持in，between，exists



**like**

like表明后面的条件时根据通配符来查询而不是确定值

%表示任何字符出现任何次(包含0个)

_表示单个字符

```mysql
where name like 'Lee%'
```





**distinct**

* 往往只用它来返回不重复记录的条数，而不是用它来返回不重记录的所有值。其原因是distinct只能返回它的目标字段
* distinct可以作用于多个字段，这样的含义是这多个字段不能同时相同
* distinct必须放在(所有)字段的前面



**分组**

分组的作用就是根据条件将数据分成多组，对这多个组同时进行聚合计算。

分组查询得到的结果是一行就是一个分组的聚合信息

注意：

* group by的主要作用是对分组进行聚合运算，所以select的目标只能是分组的条件和聚合函数
* 可以按照多个字段进行分组
* null也会分到一组



**聚合函数**

有时我们只需要统计信息而不是具体的数据。

| 聚合函数 | 描述   |
| -------- | ------ |
| AVG()    | 平均值 |
| COUNT()  | 个数   |
| MAX()    | 最大值 |
| MIN()    | 最小值 |
| SUN()    | 总和   |

聚合函数都会忽略NULL，所以在WHERE时注意去掉NULL值

聚合函数可以和IDSTINCT连用

SUM(distinct 字段名)表示值不相同的和



**Hiving**

对分组查询后的聚合结果进行过滤进行过滤



**CASE**

CASE  WHEN



**排序**

order by，对查询结果(基础查询，分组查询，子查询，联结查询)进行排序

通过order by,来指定一个或多个字段来进行排序

```mysql
order by 字段1 [desc], 字段2 [desc];
```

a和A的比较取决于数据库如何设置



## 多表查询



**别名**

多个表表名很长，可以起别名，之后原表名不能使用

```mysql
select * from
table user_info u, people_info p
where u.name=p.name
```



**笛卡尔积**

* 笛卡尔积(交叉连接,CROSS JOIN)：每一行交叉连接，缺少连接条件
* 在where中指定连接条件(等值连接与非等值连接)
* 从SQL优化的角度，建议查询的字段都带上表名



**内连接与外连接**

内连接只包含匹配到的记录，外连接表示有表的记录是必须存在的



### **内连接**

内连接可以在where中指定连接条件，也可以显示的用[inner] join连接

```
select * from tablea
join tableb on tablea.xxx *** tableb.xxx
join tableb on ......
```

另一种写法：join多张表，最后同一on

```
select * from tablea
join tableb join tablec
on tablea.xxx *** tableb.xxx and tablea.xxx *** tablec.xxx
```



**where连接与join on连接的区别**

where是SQL92的写法，on是SQL99的写法。推荐使用ON



**using**

进行等值连接,当两个字段名一样时可以用using简化

```
select * 
from tableajoin tableb 
using(xxx)
```



**非等值联结**

连接条件不再由`=`连接

比如根据薪水去联结员工等级表，薪水在某一区间的联结到某一等级



**自联结**

(等值联结)，一张表需要查询两次，就可以写成自联结,比如



### **外连接**

外连接分为左外连接，右外连接，全外连接。常见场景是"查询所有"...。MySQL不支持全外连接。

```
select * from tablea
left outer join tableb on tablea.xxx *** tableb.xxx
```

outer可以省略



**UNION ALL**

合并查询结果



**UNION**

合并查询结果，两个查询结果的列数和数据类型必须一致，去除重复部分。推荐使用UNION ALL，性能高。



**MySQL如何实现全连接**



7中JOIN的实现



**JOIN的原理**





## 子查询



规范：

* 子查询放在主查询的右侧

对一个查询结果集再进行查询，通常与in连用，也可以与=、<>使用。

**从外往里写**：先写主干，在写子查询，最后将子查询放入主干

***多个字段等于一个子查询***

```
where (x1,x2)=(select x1,x2 ...)
```



**子查询中的空值问题**

子查询的查询结果是空的时不会报错



**子查询可以的位置**

* WHERE/HIVING后面
* SELECT后
* FROM
* EXISTS(用来判断子查询有没有值)



多行子查询比较符

| 操作符 | 含义                                               |
| ------ | -------------------------------------------------- |
| IN     | 任意一个                                           |
| ANY    | 与单行比较符一起使用，与任意一个进行比较           |
| ALL    | 与单行比较符一起使用，与子查询返回的所有值进行比较 |
| SOME   | ANY的别名                                          |

* ANY/SOME/ALL都可以替代，中文比较难理解，一般用的很少这三个。
* IN后面的子查询，最好看看能不能DISTINCT



## **计算字段**

有许多存储在数据库中的值和程序需要的字段值在格式或者组合上是不一致的，需要检索出来后经过格式转换，计算等再返回给客户端。

计算字段是运行SELECT语句时创建的。



**字符串计算**

**concat()**

将括号里面的所有值拼接成一个字符串

**RTrim()**



**数字计算**

加减乘除



# DCL



## 控制增删改查的权限











Transaction Control Language

一组SQL语句组成一个单元，要么全执行，要么全失败(即失败的时候不能对数据库产生影响)。



| 命令      | 描述 |
| --------- | ---- |
| COMMIT    |      |
| ROLLBACK  |      |
| SAVEPOINT |      |
| GRANT     |      |



# 函数



**文本处理函数**

| 函数     | 描述   |
| -------- | ------ |
| Upper()  | 转大写 |
| Lower()  | 转小写 |
| Length() | 长度   |



**concat**

concat函数用来将两个列的值拼接到一起

注意：其他SQL是通过+或者||来实现拼接的

```mysql
CONCAT(str1,str2,...)
```

用列名和字符串完成拼接，可以理解成添加了一列



**trim(列名)**

用来去除空格，还有RTrim和LTrim



# 函数



函数的移植行不强，不赞成使用函数。



**字符函数**

| 函数名        | 描述                                                 |
| ------------- | ---------------------------------------------------- |
| length()      | 获取字节个数                                         |
| concat()      | 多个字符串拼接成一个                                 |
| upper()/lower | 统一大小写                                           |
| substr()      | 多个重载，用法和Java一样(字符的长度)                 |
| instar()      | 等效于Java的indexOf，没找到返回0                     |
| trim()        | 默认去除空格,  trim('a' from 'absba')表示去除前后的a |
| lpad() rpad   | 添加n个前缀/后缀                                     |
| replace()     | 替换                                                 |



SQL中的索引都是从1开始的



**数学函数**

| 函数       | 描述                                   |
| ---------- | -------------------------------------- |
| round()    | 四舍五入默认保留整数，可以指定小鼠位数 |
| cell()     | 向上取整                               |
| f'loor()   | 向下取整                               |
| truncate() | 截取                                   |
| mod        | 取余                                   |

取余

a%b=a-a/b*b



**日期函数**

| 函数        | 描述                             |
| ----------- | -------------------------------- |
| NOW()       | 返回日期时间                     |
| CURDATE()   | 返回日期                         |
| CURTIME()   | 返回时间                         |
| YEAR()      | 获得日期中的年，传入带日期的参数 |
| MONTH()     | 月                               |
| DAY()       | 日，时分秒相同                   |
| STR_TO_DATE |                                  |
| DATE_TO_STR |                                  |
| DATEDIFF    |                                  |



**其他函数**

| 方法       | 描述 |
| ---------- | ---- |
| version()  |      |
| database() |      |
| user()     |      |



# 算数运算



MySQL的算数运算就是算数运算，会将字符串转换成数字进行运算，不能转换的会被当成0；

除法返回的是浮点型数据