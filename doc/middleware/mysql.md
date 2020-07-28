# mysql

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [mysql](#mysql)
  - [InnoDB 存储引擎](#innodb-存储引擎)
  - [MyISAM 存储引擎](#myisam-存储引擎)
  - [索引](#索引)
  - [数据库事务](#数据库事务)
    - [ACID](#acid)
    - [事务并发的问题](#事务并发的问题)
    - [事务隔离级别](#事务隔离级别)
    - [mysql 默认事务隔离级别](#mysql-默认事务隔离级别)
    - [redo log](#redo-log)
    - [undo log](#undo-log)
    - [mysql MVCC（Multi-Version Concurrency Control：多版本并发控制）的实现原理](#mysql-mvccmulti-version-concurrency-control多版本并发控制的实现原理)
    - [mysql 的表锁和行锁](#mysql-的表锁和行锁)
    - [MyISAM 的锁](#myisam-的锁)
    - [InnoDB 的锁](#innodb-的锁)
  - [AUTO_INCREMENT 的原理](#auto_increment-的原理)
  - [参考](#参考)

<!-- /code_chunk_output -->

## InnoDB 存储引擎

## MyISAM 存储引擎

## 索引

## 数据库事务

### ACID

- 原性性（Actomicity）：事务是一个原子操作单元，其对数据的修改，要么全都执行，要么全都不执行。
- 一致性（Consistent）：在事务开始和完成时，数据都必须保持一致状态。这意味着所有相关的数据规则都必须应用于事务的修改，以操持完整性；事务结束时，所有的内部数据结构（如 B 树索引或双向链表）也都必须是正确的。
- 隔离性（Isolation）：数据库系统提供一定的隔离机制，保证事务在不受外部并发操作影响的“独立”环境执行。这意味着事务处理过程中的中间状态对外部是不可见的，反之亦然。
- 持久性（Durable）：事务完成之后，它对于数据的修改是永久性的，即使出现系统故障也能够保持。

### 事务并发的问题

1. 脏读（dirty read）：A 事务读取 B 事务尚未提交的更改数据，并在这个数据基础上操作。如果 B 事务回滚，那么 A 事务读到的数据根本不是合法的，称为脏读。
2. 不可重复读（unrepeatable read）：A 事务读取了 B 事务已经提交的更改（或删除）数据。比如 A 事务第一次读取数据，然后 B 事务更改该数据并提交，A 事务再次读取数据，两次读取的数据不一样。
3. 幻读（phantom read）：A 事务读取了 B 事务已经提交的新增数据。注意和不可重复读的区别，这里是新增，不可重复读是更改（或删除）。这两种情况对策是不一样的，对于不可重复读，只需要采取行级锁防止该记录数据被更改或删除，然而对于幻读必须加表级锁，防止在这个表中新增一条数据。

> 不可重复读和幻读的区别?
>
> - 不可重复读重点在于 update 和 delete
> - 幻读的重点在于 insert

### 事务隔离级别

解决事务引起的几个问题，从解决 0-3 个，一共 4 个隔离级别。

| 事务隔离级别                 | 说明               | 脏读 | 不可重复读 | 幻读 |
| :--------------------------- | :----------------- | :--- | :--------- | :--- |
| 读未提交（read-uncommitted） | 能读到未 commit 的 | 是   | 是         | 是   |
| 读提交（read-committed）     | 能读到 commit 了的 | 否   | 是         | 是   |
| 可重复读（repeatable-read）  | 重复读取，结果一样 | 否   | 否         | 是   |
| 串行化（serializable）       | 串行化             | 否   | 否         | 否   |

### mysql 默认事务隔离级别

可重复读，MySQL 的可重复读隔离级别解决了幻读问题。是通过行锁和间隙锁的组合 Next-Key 锁实现的。

### redo log

### undo log

undo log 是 MVCC 实现的一个重要依赖

### mysql MVCC（Multi-Version Concurrency Control：多版本并发控制）的实现原理

1. InnoDB 里面每个事务有一个唯一的事务 ID，叫作 transaction id。它是在事务开始的时候向 InnoDB 的事务系统申请的，是按申请顺序严格递增的。
2. 对于数据库的每行记录，都会有三个隐藏字段：db_trx_id (事务 id)、db_roll_pt (回滚指针)、delete_flag(删除标记)。根据这几个字段，组成事务链。
3. INSERT：创建一条数据，db_trx_id 的值为当前事务 id, db_roll_pt 为 null 。
4. UPDATE：复制修改一行数据，将当前复制后这一行的 db_trx_id 置为当前事务的 id，db_roll_pt 是一个指针，指向复制前的那一条的，旧数据在 undo log 里面。
5. DELETE：复制一行数据，将当前复制后这一行的 db_trx_id 置为当前事务的 id，db_roll_pt 是一个指针，指向复制前的那一条的。并把 delete_flag 置为 true 。
6. SELECT：根据查询时的事务 id,作为版本号，与数据库中的版本比较。

已提交读隔离级别下的事务在每次查询的开始都会生成一个独立的 ReadView。
可重复读隔离级别则在第一次读的时候生成一个 ReadView，之后的读都复用之前的 ReadView。

### mysql 的表锁和行锁

- 表级锁：每次操作锁住整张表。开销小，加锁快；不会出现死锁；锁定粒度大，发生锁冲突的概率最高，并发度最低；
- 行级锁：每次操作锁住一行数据。开销大，加锁慢；会出现死锁；锁定粒度最小，发生锁冲突的概率最低，并发度也最高；

### MyISAM 的锁

### InnoDB 的锁

## AUTO_INCREMENT 的原理

## 参考

[mysql 锁](https://www.cnblogs.com/deliver/p/5730616.html)
