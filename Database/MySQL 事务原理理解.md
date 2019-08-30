本文为 极客时间 丁奇老师 的《 MySQL 实战45讲》课程 学习笔记

----



## 0. 事务 -- transaction

事务要保证一组数据库操作， 要么全部成功，要么全部失败； 保证了数据一致性。



查看引擎是否支持 事务：

```SQL
mysql> SHOW ENGINES
    -> ;
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
| Engine             | Support | Comment                                                        | Transactions | XA   | Savepoints |
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
| ARCHIVE            | YES     | Archive storage engine                                         | NO           | NO   | NO         |
| BLACKHOLE          | YES     | /dev/null storage engine (anything you write to it disappears) | NO           | NO   | NO         |
| MRG_MYISAM         | YES     | Collection of identical MyISAM tables                          | NO           | NO   | NO         |
| FEDERATED          | NO      | Federated MySQL storage engine                                 | NULL         | NULL | NULL       |
| MyISAM             | YES     | MyISAM storage engine                                          | NO           | NO   | NO         |
| PERFORMANCE_SCHEMA | YES     | Performance Schema                                             | NO           | NO   | NO         |
| InnoDB             | DEFAULT | Supports transactions, row-level locking, and foreign keys     | YES          | YES  | YES        |
| MEMORY             | YES     | Hash based, stored in memory, useful for temporary tables      | NO           | NO   | NO         |
| CSV                | YES     | CSV storage engine                                             | NO           | NO   | NO         |
+--------------------+---------+----------------------------------------------------------------+--------------+------+------------+
9 rows in set (0.01 sec)

```






-----



## 1. 事务的特性 ： ACID

1. A： 原子性 Atomicity ， 事务是进行数据处理的基本单位


2. C 一致性 Consistency ，数据库进行实务操作之后，由原来的一致状态变成另一种一致状态。

3. D 持久性 Durability ， 事务提交之后，对数据的修改是持久性的， 当事务完成之后，数据库的日志就会更新， 通过日志，恢复数据库。 事务提交之后就没有办法再进行回滚了
> 唯一能够撤回已经提交的事务的方式是，创建一个相反的事务操作




事务的状态图：

![](https://i.loli.net/2019/07/21/5d34166e7ea8e59406.png)
> 图片来源 参考2


1. Active ： 事务初始状态， 表示事务正在执行

2. Patially Commited : 最后一条操作语句执行之后

3. Failed : 事务无法正常执行

4. Aborted ： 事务被 回滚并且数据库恢复到事务之前的状态

5. Commited ： 成功执行事务






## 2. 事务并发处理困难存在的异常：

存在一个数据表 记录三国中的英雄

|  id   | name  |
|  ----  | ----  |
| 1  | 刘备 |
| 2 | 关羽 |
|3 | 张飞|

假设不进行事务隔离


### 1. 脏读  Dirty read

1. 小甲 对数据库做了一个更改， 例如 ： 加入一个新的 英雄
2. 小甲没有提交事务之前，小乙访问数据库， 查看数据库表的英雄， 却能看到 小甲新加入的英雄，


这种现象就是脏读 --- 读到了其他事务还没提交的数据



### 2.  不可重复读

1. 小甲 对数据库做一个查询
```sql
SELECT name FROM sanguo WHERE id = 1;
```
查询到 英雄 刘备


2. 这时候， 小乙开始一个事务操作， 对 id = 1 的英雄姓名进行了修改


3. 然后小甲再一次进行了查询， 查询 id = 1， 得到的结果是小乙刚刚修改之后

4. 小甲两次查询的结果不相同，

两次读取的结果不同


### 3. 幻读

1. 小甲对整个数据表进行了查询

```sql

SELECT * FROM sanguo;
```

2. 这时候， 小乙开启一个新的事务， 向数据库里面插入英雄

3. 小甲再次对整个数据库表进行查询， 这次查询 多了一条数据


-----


## 3. MySQL 模拟上述三种异常

### 1. 先按照表格 创建数据库表

|  id   | name  |
|  ----  | ----  |
| 1  | 刘备 |
| 2 | 关羽 |
|3 | 张飞|


1. 查看数据库现有的表格

```sql
mysql> SHOW TABLES
    -> ;
+--------------------+
| Tables_in_students |
+--------------------+
| sanguo             |
| students           |
| user               |
+--------------------+
3 rows in set (0.00 sec)
```

2. 创建数据库表 hero


```sql
mysql> CREATE TABLE hero
    -> (
    -> id int NOT NULL,
    -> name varchar(255) NOT NULL,
    -> PRIMARY KEY (id) USING BTREE)
    -> ;
Query OK, 0 rows affected (0.02 sec)

```

3. 查看创建好的 表的结构

```sql
mysql> DESC hero
    -> ;
+-------+--------------+------+-----+---------+-------+
| Field | Type         | Null | Key | Default | Extra |
+-------+--------------+------+-----+---------+-------+
| id    | int(11)      | NO   | PRI | NULL    |       |
| name  | varchar(255) | NO   |     | NULL    |       |
+-------+--------------+------+-----+---------+-------+
2 rows in set (0.01 sec)

```

4. 插入数据


```sql
mysql> INSERT INTO hero VALUES(1,'刘备');
Query OK, 1 row affected (0.01 sec)

mysql> INSERT INTO hero VALUES(2,'关羽'),(3,'张飞');
Query OK, 2 rows affected (0.01 sec)
Records: 2  Duplicates: 0  Warnings: 0


```

5. 查看 已经建立好的数据表


```sql
mysql> SELECT * FROM hero;
+----+--------+
| id | name   |
+----+--------+
|  1 | 刘备   |
|  2 | 关羽   |
|  3 | 张飞   |
+----+--------+
3 rows in set (0.00 sec)

```


### 2. 客户端设置

**需要两个客户端**

1. 查看当前的默认隔离级别

是 可重复读


```sql
mysql> SHOW VARIABLES LIKE 'transaction_isolation';
+-----------------------+-----------------+
| Variable_name         | Value           |
+-----------------------+-----------------+
| transaction_isolation | REPEATABLE-READ |
+-----------------------+-----------------+
1 row in set (0.04 sec)

```

2. 更改为 隔离级别最低的 读未提交


```sql
mysql> SET SESSION TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW VARIABLES LIKE 'transaction_isolation';
+-----------------------+------------------+
| Variable_name         | Value            |
+-----------------------+------------------+
| transaction_isolation | READ-UNCOMMITTED |
+-----------------------+------------------+
1 row in set (0.00 sec)

```

3. MySQL 默认是 事务自动提交， 修改

```sql
mysql> SET autocommit = 0;
Query OK, 0 rows affected (0.00 sec)

mysql> SHOW VARIABLES LIKE 'autocommit';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| autocommit    | OFF   |
+---------------+-------+
1 row in set (0.00 sec)

```


4. **客户端2 也进行相同的配置**



### 3. 模拟脏读

1. 在客户端2 开启一个新的事务 -- 插入 一条新的数据，但是没有提交

```sql
mysql> BEGIN ;
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO hero VALUES(4,'吕布');
Query OK, 1 row affected (0.00 sec)

```


2.  在客户端 1 查看当前的 数据库表

```sql
mysql> SELECT * FROM hero;
+----+--------+
| id | name   |
+----+--------+
|  1 | 刘备   |
|  2 | 关羽   |
|  3 | 张飞   |
|  4 | 吕布   |
+----+--------+
4 rows in set (0.00 sec)

```

发现 客户端1 读取到了 客户端 2 未提交的数据，



### 4. 模拟 不可重复读

1. 在客户端1 进行查询

```sql
mysql> SELECT name FROM hero WHERE id = 1;
+--------+
| name   |
+--------+
| 刘备   |
+--------+
1 row in set (0.00 sec)

```

2. 在客户端2 进行查询的数据修改

```sql
mysql> BEGIN;
Query OK, 0 rows affected (0.00 sec)

mysql> UPDATE hero SET name = '刘皇叔' WHERE id = 1;
Query OK, 1 row affected (0.02 sec)
Rows matched: 1  Changed: 1  Warnings: 0

```

3. 再次在客户端1 进行查询

```sql
mysql> SELECT name FROM hero WHERE id = 1;
+-----------+
| name      |
+-----------+
| 刘皇叔    |
+-----------+
1 row in set (0.01 sec)

```

得到了 不同的结果


### 5. 模拟  幻 读

客户端1， 查询

```sql
mysql> SELECT * FROM hero;
+----+--------+
| id | name   |
+----+--------+
|  1 | 刘备   |
|  2 | 关羽   |
|  3 | 张飞   |
+----+--------+
3 rows in set (0.00 sec)

```

客户端 2 插入新的数据

```sql
mysql> INSERT INTO hero VALUES(4,'吕布');
Query OK, 1 row affected (0.00 sec)

```

客户端1 再次进行查询


```sql

mysql> SELECT * FROM hero;
+----+--------+
| id | name   |
+----+--------+
|  1 | 刘备   |
|  2 | 关羽   |
|  3 | 张飞   |
|  4 | 吕布   |
+----+--------+
4 rows in set (0.00 sec)
```

发现多了一条数据




### 6. 模拟之后 退出客户端

再次连接数据库，查看配置， 恢复默认


```sql
mysql> SHOW VARIABLES LIKE 'transaction_isolation';
+-----------------------+-----------------+
| Variable_name         | Value           |
+-----------------------+-----------------+
| transaction_isolation | REPEATABLE-READ |
+-----------------------+-----------------+
1 row in set (0.01 sec)

mysql> show variables like 'autocommit';
+---------------+-------+
| Variable_name | Value |
+---------------+-------+
| autocommit    | ON    |
+---------------+-------+
1 row in set (0.01 sec)

```



## 4. 隔离性

1. 当数据库上面有多个事务同时执行的时候， 出现很多问题，未处理这些问题， 就有了 隔离级别


2. 隔离的越严 ， 执行的效率就越低

3. 事务隔离的级别：


![](https://i.loli.net/2019/07/19/5d312c54cb3fb97967.png)

* 读未提交： 事务还没提交的时候， 做的能被别的事务看到
> 事务 B的值虽然还没有提交， 但是结果 A已经可以看到， 所以 V1，V2，V3 d的结果都是 2



* 读提交 ： 事务提交之后， 做的变更才能被别的事务看到
> 事务 B 的更新只有在提交之后才能被 A 看到， 所以 V1 是1 ， V2.V3 是 2


* 可重复读 ： 事务执行的时候看到的数据和 在事务启动的时候看到的数据是一致的 (别人改数据的事务已经提交，在自己的事务中也不读取)

> V1= V2 = 1, V2 = 3, 因为要求事务 A 在执行期间看到的数据前后必须一致


* 串行化 ： 对于同一行记录， 写入会加上 写锁， 读会加上 读锁， 当出现读写冲图的时候， 后访问的事务必须等到前一个事务执行完成才能继续执行。（自己的事没有提交，别人 不可以更改数据）

> V1= V2 = 1, V2 = 3 ， 因为 事务B 的修改事务，会被锁住，直到事务A 提交之后，事务B 才能继续执行


**并行性能依次降低， 安全性能依次升高**


隔离级别解决异常情况：

![](https://i.loli.net/2019/07/21/5d34166e7ea8e59406.png)






4. MySQL 默认 隔离级别  可重复读

```sql

mysql> mysql> show variabl 'transaction_isolation';


+-----------------------+-----------------+
| Variable_name         | Value           |
+-----------------------+-----------------+
| transaction_isolation | REPEATABLE-READ |
+-----------------------+-----------------+
1 row in set (0.42 sec)

```


5. 修改 隔离级别

```sql

set global transaction isolation level READ COMMITTED ;
set session transaction isolation level READ COMMITTED ;

```

-----


## 5. 事务隔离的实现

每条记录在更新的时候都会记录一条回滚操作，通过回滚操作可以得到前一个状态的值。

![](https://i.loli.net/2019/07/19/5d312daeaa44495578.png)

上图就是一个值 1， 依次更新成 2，3，4


1. 不同时刻启动的事务 会有不同的 read_view ， 同一个记录在系统中存在多个版本， 数据库的多版本并发控制。
> 在可重复读（REPEATABLE READ，简称RR）隔离级别下，数据快照（read_view）版本是在第一个读请求发起时创建的
即当事务中的第一个SELECT请求才创建read view。


2. 当没有事务需要用到回滚日志的时候，回滚日志会被删除， 即 系统里没有比 回滚日志更早的 read -view 的时候

3. 尽量不要使用长事务：
存在很老的事务视图， 数据库会保存他所有可能用到的回滚记录， 占用大量的空间


----

## 6. 事务的启动方式

1. 显式的启动，
```sql
begin

start transaction

commit

commit work and chain   ##提交事务 自动开启下一个事务

```

2.
```sql
set autocommit = 0

```
使用 select 事务就会启动，不会自动提交， 直达主动执行 commit



**建议使用 set autocommit = 1 ， 显式语句启动事务**


3.  查询长事务 时间超过 60s

```sql

select * from information_schema.innodb_trx where TIME_TO_SEC(timediff(now(),trx_started))>60


```

----

## 7. 事务的控制

```sql
mysql> CREATE TABLE sanguo(name varchar(255),PRIMARY KEY (name)) ENGINE = InnoDB;
Query OK, 0 rows affected (0.01 sec)

mysql> BEGIN;
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO sanguo SELECT '刘备';
Query OK, 1 row affected (0.00 sec)
Records: 1  Duplicates: 0  Warnings: 0

mysql> COMMIT;
Query OK, 0 rows affected (0.01 sec)

mysql> BEGIN;
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO sanguo SELECT '关羽';
Query OK, 1 row affected (0.00 sec)
Records: 1  Duplicates: 0  Warnings: 0

mysql> INSERT INTO sanguo SELECT '关羽';
ERROR 1062 (23000): Duplicate entry '关羽' for key 'PRIMARY'
mysql> ROLLBACK;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT * FROM sanguo;
+--------+
| name   |
+--------+
| 刘备   |
+--------+
1 row in set (0.00 sec)


```


* 上面的代码中 SQL 执行了2 个事务， 一个是 插入数据“刘备” 提交之后执行成功

* 第二个事务是，插入两次 相同的数据 ”关羽“

* 由于设置了name 是主键， 主键是唯一的， 所以第二次插入数据 “ 关羽“ 就会报错，导致事务 失败

* 执行 ROLLBACK ，对事务进行回滚， 回到第一个事务执行完成

* 结果只有一条数据


-------






```sql

mysql> DROP TABLE sanguo;
Query OK, 0 rows affected (0.01 sec)


mysql> CREATE TABLE sanguo(name varchar(255),PRIMARY KEY (name)) ENGINE = InnoDB;
Query OK, 0 rows affected (0.01 sec)

mysql> BEGIN;
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO sanguo SELECT '刘备';
Query OK, 1 row affected (0.00 sec)
Records: 1  Duplicates: 0  Warnings: 0

mysql> COMMIT;
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO sanguo SELECT '关羽';
Query OK, 1 row affected (0.00 sec)
Records: 1  Duplicates: 0  Warnings: 0

mysql> INSERT INTO sanguo SELECT '关羽';
ERROR 1062 (23000): Duplicate entry '关羽' for key 'PRIMARY'
mysql> ROLLBACK;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT * FROM sanguo;
+--------+
| name   |
+--------+
| 关羽   |
| 刘备   |
+--------+
2 rows in set (0.00 sec)

```

* 没有使用 BEGIN 显式 的开启事务， 对于 MySQL 默认会进行自动提交， 插入 关羽之后 ，数据表里面就已经存在两行数据


* 如果使用 BEGIN 显式的开启事务，必须 commit 才会生效

* 第二次插入 ”关羽“ 会报错

* ROLLBACK 事务已经自动提交了， 没办法回滚

-----


```sql

mysql> DROP TABLE sanguo;
Query OK, 0 rows affected (0.01 sec)

mysql> CREATE TABLE sanguo(name varchar(255),PRIMARY KEY (name)) ENGINE = InnoDB;
Query OK, 0 rows affected (0.01 sec)

mysql> SET @@completion_type = 1;
Query OK, 0 rows affected (0.00 sec)

mysql> BEGIN;
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO sanguo SELECT '刘备';
Query OK, 1 row affected (0.00 sec)
Records: 1  Duplicates: 0  Warnings: 0

mysql> COMMIT;
Query OK, 0 rows affected (0.00 sec)

mysql> INSERT INTO sanguo SELECT '关羽';
Query OK, 1 row affected (0.00 sec)
Records: 1  Duplicates: 0  Warnings: 0

mysql> INSERT INTO sanguo SELECT '关羽';
ERROR 1062 (23000): Duplicate entry '关羽' for key 'PRIMARY'
mysql> ROLLBACK;
Query OK, 0 rows affected (0.00 sec)

mysql> SELECT * FROM sanguo;
+--------+
| name   |
+--------+
| 刘备   |
+--------+
1 row in set (0.00 sec)


```

* 两次插入 数据 “关羽” 的操作，认为是同一个事务之内的操作， 第二次插入失败，导致事务失败， 回滚进行了撤销操作


**completion_type 的作用**

1.  completion_type = 0 ， 默认

COMMIT 会提交事务， 执行下一个事务，需要使用 BIEGIN 开启

2. completion_type = 1，

提交事务之后， 相当于执行了 COMMIT AND CHAIN, 开始链式事务： 提交一个事务，开启一个相同隔离级别的事务

3. completion_type = 2 ， 相当于  COMMIT AND RELEASE ，提交之后 自动和服务器断开连接










---
# 参考

1. http://www.voidcn.com/article/p-datyyplc-bnz.html

2. 『浅入深出』MySQL 中事务的实现 ---- https://draveness.me/mysql-transaction


3. 初识事务隔离 --- https://time.geekbang.org/column/article/107401

4.

