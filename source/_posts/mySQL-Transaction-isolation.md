---
title: mySQL_Transaction_isolation
date: 2016-04-09 10:24:59
tags:
---
Innodb中的事务隔离级别和锁的关系
============
#####事务四种性质
+ 原子性
+ 一致性
+ 隔离性
+ 持久性

##### <em>尤其是一致性和隔离性</em>一般是使用加锁的方式。<strong>但是过度加锁会降低并发处理的能力</strong>
-------------------------------------------------------------------------------
>一次加锁 or 两次加锁？
>因为有大量的访问，为了预防死锁，一般应用中推荐使用一次封锁法（就是在方法开始的阶段），已经预先知道会用到哪些数据，让后全部锁住，在方法运行之后，再全部解锁，这种方式可以有效避免循环死锁，但是不适用于数据库，因为在事务开始阶段，数据库并不知道会用到哪些数据库
>
------------------------------------


数据库采用的是两段锁协议，将事务分成两个阶段，加锁阶段和解锁阶段

+ <strong>加锁阶段</strong> :在该阶段可以进行加锁操作，在任何数据进行读操作之前要申请并获得S锁（共享锁，其他事务可以继续加上共享锁，但不能加上排他锁），在进行写操作之前申请并获得X锁(排他锁，其他事务不能在获得任何锁)，加锁不成功，则事务要进行等待状态，直到加锁成功才继续执行。
+ <strong>解锁阶段</strong>当事务释放了一个加锁以后，事务进入解锁阶段，<em>在该阶段只能进行解锁操作不能再进行任何加锁操作</em>
---------------------------------------
|事务        | 加锁/解锁处理      
|------------ | :-----------:|
|begin;       |        nothing to do       | 
|insert into | 加insert对应的锁|       
|update ... set      | 加update对应的锁|
|delete from |      加delete对应的锁 |
|commit;     | 事务提交的时候，同时释放insert,update,delete 对应的锁|
这种方式虽然无法避免死锁，但是两段锁协议可以保证事务的并发调度是串行化（串行化很重要，尤其是在数据恢复和备份的时候）的。

###事务中加锁的方式:
#####事务的四种隔离级别
--------------------
>在数据库操作中，为了保证并发读取数据的正确性，提出事务隔离的级别。<stong>我们的数据库锁，也是构建这些隔离级别的存在</strong>
>
-------------------
-------------------
|隔离级别|脏读（dirty read）|不可重复读（NonRepeatable Read）|幻读（Phantom Read）
|--------------------|:----------------------:|:-----------:|:-------------:|
|未提交读（Read uncommitted）|可能|可能|可能|
|已提交读（Read committed）|不可能|可能|可能|
|可重复读（nRepeatable Read）|不可能|不可能|可能|
|可串行化（serialable）|不可能|不可能|不可能|
+ 未提交读（Read uncommitted）：允许脏读，也就是可能读取到其他会话中未提交事务修改数据
+ 提交读（Read committed）：只能读取到已经提交到的数据，Oracle等数据库默认都是该级别
+ 可重复读（nRepeatable Read）：可重复读，在同一个事务内查询都是事务开始时刻是一致的，InnoDB默认级别，在SQL标准中，该隔离级别消除了不可重复读，但是还是存在幻读
+ 串行读（serialzable）：完全串行化的读，每次读都需要获得表级共享锁，读写相互都会阻塞。
+ ① 脏读: 脏读就是指当一个事务正在访问数据，并且对数据进行了修改，而这种修改还没有提交到数据库中，这时，另外一个事务也访问这个数据，然后使用了这个数据。
+ ② 不可重复读:是指在一个事务内，多次读同一数据。在这个事务还没有结束时，另外一个事务也访问该同一数据。那么，在第一个事务中的两次读数据之间，由于第二个事务的修改，那么第一个事务两次读到的的数据可能是不一样的。这样就发生了在一个事务内两次读到的数据是不一样的，因此称为是不可重复读
+ ④ 幻读:第一个事务对一个表中的数据进行了修改，这种修改涉及到表中的全部数据行。同时，第二个事务也修改这个表中的数据，这种修改是向表中插入一行新数据。那么，以后就会发生操作第一个事务的用户发现表中还有没有修改的数据行，就好象发生了幻觉一样。


