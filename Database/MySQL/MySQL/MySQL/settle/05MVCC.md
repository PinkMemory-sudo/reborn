[JavaGuild](https://github.com/PinkMemory-sudo/JavaGuide/blob/master/docs/database/InnoDB%E5%AF%B9MVCC%E7%9A%84%E5%AE%9E%E7%8E%B0.md)

**读当前和读快照**



**为什么要用 MVCC**

用锁来并发控制时，只有读读可以并发，读写，写读和写写都需要阻塞，使用 MVCC来解决读写重读，只需要写写时阻塞。



**一致性非锁定读**

通常的做法是加版本号或者时间戳，更新数据时版本号+1 或者更新时间戳，将当前课件版本号与记录的版本号对比，如果记录的版本号小于课件版本号，则表示记录可见

可见版本号？

记录版本号？



InnoDB 中，当读取的数据正在进行 DELETE 或者 UPDATE 时，不会去等待锁的释放，而是去读取快照数据



在可重读和读已提交隔离级别下，执行普通的 select(不包含 lock...)则会使用到 MVCC，可重读隔离级别下，MVCC 防止了部分幻读，Read View在第一次查询时生成，但如果是`当前读` ，每次读取的都是最新数据，这时如果两次查询中间有其它事务插入数据，就会产生幻读。**所以 `InnoDB` 在实现`Repeatable Read` 时，如果执行的是当前读，则会对读取的记录使用 `Next-key Lock` ，来防止其它事务在间隙间插入数据**



如果执行的是下列语句，就是 锁定读（Locking Reads）

select ... lock in share mode
select ... for update
insert、update、delete 操作

锁定读读取到的是当前最新的数据



# **MVCC 实现原理**

MVCC 依赖于隐藏字段，ReadView，undo log。`InnoDB` 通过数据行的 `DB_TRX_ID` 和 `Read View` 来判断数据的可见性，如不可见，则通过数据行的 `DB_ROLL_PTR` 找到 `undo log` 中的历史版本。每个事务读到的数据版本可能是不一样的，在同一个事务中，用户只能看到该事务创建 `Read View` 之前已经提交的修改和该事务本身做的修改



**隐藏字段：**

InnoDB为每行数据添加了三个隐藏字段

DB_TRX_ID：表示最后一次插入或更新改行的事务 ID，此外，`delete` 操作在内部被视为更新，只不过会在记录头 `Record header` 中的 `deleted_flag` 字段将其标记为已删除。

DB_ROLL_PTR：回滚字段，指向该行的 `undo log` 。如果该行未被更新，则为空

DB_ROW_ID：如果没有设置主键且该表没有唯一非空索引时，`InnoDB` 会使用该id来生成聚簇索引



**ReadView：**

主要是用来做可见性判断，里面保存了 “当前对本事务不可见的其他活跃事务”

主要有以下字段：

m_low_limit_id：目前出现过的最大的事务ID+1，即下一个将被分配的事务ID。大于这个ID的数据版本均不可见
m_up_limit_id：活跃事务列表 m_ids 中最小的事务ID，如果 m_ids 为空，则 m_up_limit_id 为 m_low_limit_id。小于这个ID的数据版本均可见
m_ids：Read View 创建时其他未提交的活跃事务ID列表。创建 Read View 时，将当前未提交事务ID记录下来，后续即使它们修改了记录行的值，对于当前事务也是不可见的。m_ids 不包括当前事务自己和已提交的事务（正在内存中）
m_creator_trx_id：创建该 Read View 的事务ID

**undo-log**
undo log 主要有两个作用：

当事务回滚时用于将数据恢复到修改前的样子
另一个作用是 MVCC ，当读取记录时，若该记录被其他事务占用或当前版本对该事务不可见，则可以通过 undo log 读取之前的版本数据，以此实现非锁定读



**小于什么 ID 不可见？**

可见性算法





**在 `InnoDB` 存储引擎中 `undo log` 分为两种： `insert undo log` 和 `update undo log`：**

1. **`insert undo log`** ：指在 `insert` 操作中产生的 `undo log`。因为 `insert` 操作的记录只对事务本身可见，对其他事务不可见，故该 `undo log` 可以在事务提交后直接删除。不需要进行 `purge` 操作
2. **`update undo log`** ：`update` 或 `delete` 操作中产生的 `undo log`。该 `undo log`可能需要提供 `MVCC` 机制，因此不能在事务提交时就进行删除。提交时放入 `undo log` 链表，等待 `purge线程` 进行最后的删除







活跃事务，事务的可见性