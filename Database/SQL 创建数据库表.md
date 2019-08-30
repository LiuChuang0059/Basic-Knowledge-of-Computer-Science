
## 1. 查看已有的数据库


```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.01 sec)

```

可以看到， 我的这里默认存在 4 个 数据库



---

## 2.  创建 数据库


```sql

mysql> create database students;
Query OK, 1 row affected (0.01 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| students           |
| sys                |
+--------------------+
5 rows in set (0.00 sec)

```

可以看到，数据库中多了我们刚刚创建的 students


---

## 3. 创建 数据库表

MySQL 数据库， 数据库， 数据库表，三者关系

![](https://i.loli.net/2019/07/14/5d2a98375dff660668.png)

1. 选择 要操作的数据库， 这里选择我们刚刚创建的 studens

```python

mysql> use students;
Database changed

```

2. 创建一个下面这样的 简单的表

| id | name | score | gender |
| :-----| ----: | :----: | :----:|
| 1 | liu | 90 | male|
| 2 | zou | 95 | female|


```python

mysql> create table student_test
    -> (
    -> id char(10) not null primary key,
    -> name char(20) not null,
    -> score int not null,
    -> gender char(6) not null
    -> );
Query OK, 0 rows affected (0.02 sec)

```

*  create table student_test : 创建一个 数据库表名叫 student_test 的 数据表

* id char(10) not null primary key, 创建一列， 名称是 id， 数据类型是 char， 字符的最大长度是 10， 内容 不能是 NULL ， 同时这一列设置为 数据库的主键
> 根据之前我们说的， NULL 存在会影响数据库的效率

* score int not null, 创建一列， 名称是 score ， 数据类型是，int 整型


3. 创建之后，检查一下创建的表

```sql

mysql> describe student_test
    -> ;
+--------+----------+------+-----+---------+-------+
| Field  | Type     | Null | Key | Default | Extra |
+--------+----------+------+-----+---------+-------+
| id     | char(10) | NO   | PRI | NULL    |       |
| name   | char(20) | NO   |     | NULL    |       |
| score  | int(11)  | NO   |     | NULL    |       |
| gender | char(6)  | NO   |     | NULL    |       |
+--------+----------+------+-----+---------+-------+
4 rows in set (0.01 sec)

```

和我们预想的一样

* describe 查看的是表的结构 ： 列的名称和类型等

Field ： 字段名， Type ： 数据类型，  NULL ： 是否可以为空， Key 是否是 主键，  Default ： 默认值， Extra ： 扩展属性

---

## 4. 向数据库表中添加 数据

1. INSERT 基本语法

```sql

mysql> insert into student_test values("1", "liu",90,"male");
Query OK, 1 row affected (0.01 sec)

mysql> insert into student_test values("2", "zou",95,"female");
Query OK, 1 row affected (0.01 sec)

```
查看 刚刚创建的数据库表中的 数据


```sql

mysql> select * from student_test
    -> ;
+----+------+-------+--------+
| id | name | score | gender |
+----+------+-------+--------+
| 1  | liu  |    90 | male   |
| 2  | zou  |    95 | female |
+----+------+-------+--------+
2 rows in set (0.00 sec)

```


" * " 代表查询所有列的数据，就是我们期望的整张表的数据

表格内容和我们预期的 一样


2. 加入数据的时候， 可以一次添加多条

```sql
mysql> INSERT INTO student_test (id,name,score,gender,class_id) VALUES
('4','li',80,"male","1"),
('5','wang',85,"male","2");

Query OK, 2 rows affected (0.01 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> SELECT * FROM student_test;
+----+-------+-------+--------+----------+
| id | name  | score | gender | class_id |
+----+-------+-------+--------+----------+
| 1  | liu   |    90 | male   | 1        |
| 2  | zou   |    95 | female | 2        |
| 3  | zhang |    85 | male   | 2        |
| 4  | li    |    80 | male   | 1        |
| 5  | wang  |    85 | male   | 2        |
+----+-------+-------+--------+----------+
5 rows in set (0.00 sec)
```

----

## 5.更新表中的数据

1. 基本语法

```sql
UPDATE <表名> SET 字段1=值1, 字段2=值2, ... WHERE ...;
```

2. 使用 WHERE 筛选条件 一次修改多个数据

> 对于 表格中 二班的成绩全部调整称为  90分



```sql
mysql> UPDATE student_test SET score = 90 WHERE class_id = 2 ;
Query OK, 3 rows affected (0.00 sec)
Rows matched: 3  Changed: 3  Warnings: 0

mysql> SELECT * FROM student_test;
+----+-------+-------+--------+----------+
| id | name  | score | gender | class_id |
+----+-------+-------+--------+----------+
| 1  | liu   |    90 | male   | 1        |
| 2  | zou   |    90 | female | 2        |
| 3  | zhang |    90 | male   | 2        |
| 4  | li    |    80 | male   | 1        |
| 5  | wang  |    90 | male   | 2        |
+----+-------+-------+--------+----------+
5 rows in set (0.00 sec)

```

3. 更新字段的时候 可以使用表达式

> 对于 表格中 一班的成绩全部 减 10

```sql
mysql> UPDATE student_test SET score = score - 10  WHERE class_id = 1 ;
Query OK, 2 rows affected (0.00 sec)
Rows matched: 2  Changed: 2  Warnings: 0

mysql> SELECT * FROM student_test;
+----+-------+-------+--------+----------+
| id | name  | score | gender | class_id |
+----+-------+-------+--------+----------+
| 1  | liu   |    80 | male   | 1        |
| 2  | zou   |    90 | female | 2        |
| 3  | zhang |    90 | male   | 2        |
| 4  | li    |    70 | male   | 1        |
| 5  | wang  |    90 | male   | 2        |
+----+-------+-------+--------+----------+
5 rows in set (0.00 sec)
```



4. 注意 ⚠️ ： 如果没加 WHERE 条件， 整个数据表都会进行更新

最好先 WHERE 测试筛选结果，再更新

```sql
mysql> UPDATE student_test SET score = 100;
Query OK, 5 rows affected (0.00 sec)
Rows matched: 5  Changed: 5  Warnings: 0

mysql> SELECT * FROM student_test;
+----+-------+-------+--------+----------+
| id | name  | score | gender | class_id |
+----+-------+-------+--------+----------+
| 1  | liu   |   100 | male   | 1        |
| 2  | zou   |   100 | female | 2        |
| 3  | zhang |   100 | male   | 2        |
| 4  | li    |   100 | male   | 1        |
| 5  | wang  |   100 | male   | 2        |
+----+-------+-------+--------+----------+
5 rows in set (0.00 sec)

```

----

## 6. 删除数据库中的记录

1. 基本语法
```sql
DELETE FROM <表名> WHERE ...;

```
删除  2 班学生的记录


```sql
mysql> DELETE FROM student_test WHERE class_id = 2;
Query OK, 3 rows affected (0.01 sec)

mysql> SELECT * FROM student_test;
+----+------+-------+--------+----------+
| id | name | score | gender | class_id |
+----+------+-------+--------+----------+
| 1  | liu  |   100 | male   | 1        |
| 4  | li   |   100 | male   | 1        |
+----+------+-------+--------+----------+
2 rows in set (0.00 sec)

```

2. 特别小心：

不加 WHERE 会将整个表格都删除

-----


## 7.  修改数据库表 -- 本身的结构

目前数据表的结构

```sql
mysql> SELECT * FROM student_test;
+----+------+-------+--------+----------+
| id | name | score | gender | class_id |
+----+------+-------+--------+----------+
| 1  | liu  |   100 | male   | 1        |
| 2  | lin  |    80 | male   | 1        |
| 4  | li   |   100 | male   | 1        |
+----+------+-------+--------+----------+
3 rows in set (0.00 sec)

```


### 1. 修改数据表的列

1. 修改列名
name 改成 sname

```sql

mysql> ALTER TABLE student_test change name sname char(10) NOT NULL;
Query OK, 3 rows affected (0.04 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> DESC student_test;
+----------+----------+------+-----+---------+-------+
| Field    | Type     | Null | Key | Default | Extra |
+----------+----------+------+-----+---------+-------+
| id       | char(10) | NO   | PRI | NULL    |       |
| sname    | char(10) | NO   |     | NULL    |       |
| score    | int(11)  | NO   |     | NULL    |       |
| gender   | char(6)  | NO   |     | NULL    |       |
| class_id | char(10) | NO   |     | NULL    |       |
+----------+----------+------+-----+---------+-------+
5 rows in set (0.01 sec)

```


2. 修改 列的 数据类型

id 修改 为 int

```sql
mysql> ALTER TABLE student_test CHANGE id  id int NOT NULL;
Query OK, 3 rows affected (0.03 sec)
Records: 3  Duplicates: 0  Warnings: 0

mysql> DESC student_test;
+----------+----------+------+-----+---------+-------+
| Field    | Type     | Null | Key | Default | Extra |
+----------+----------+------+-----+---------+-------+
| id       | int(11)  | NO   | PRI | NULL    |       |
| sname    | char(10) | NO   |     | NULL    |       |
| score    | int(11)  | NO   |     | NULL    |       |
| gender   | char(6)  | NO   |     | NULL    |       |
| class_id | char(10) | NO   |     | NULL    |       |
+----------+----------+------+-----+---------+-------+
5 rows in set (0.00 sec)
```


### 2. 删除 列

```sql
mysql> ALTER TABLE student_test DROP class_id;
Query OK, 0 rows affected (0.14 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> DESC student_test;
+--------+----------+------+-----+---------+-------+
| Field  | Type     | Null | Key | Default | Extra |
+--------+----------+------+-----+---------+-------+
| id     | int(11)  | NO   | PRI | NULL    |       |
| sname  | char(10) | NO   |     | NULL    |       |
| score  | int(11)  | NO   |     | NULL    |       |
| gender | char(6)  | NO   |     | NULL    |       |
+--------+----------+------+-----+---------+-------+
4 rows in set (0.00 sec)

```

### 3. 重命名数据库表
```sql
mysql> ALTER TABLE student_test RENAME students;
Query OK, 0 rows affected (0.01 sec)

mysql> SHOW TABLES
    -> ;
+--------------------+
| Tables_in_students |
+--------------------+
| class_test         |
| students           |
+--------------------+
2 rows in set (0.00 sec)
```

---

### 4. 删除数据库表

```sql
mysql> DROP TABLE class_test;
Query OK, 0 rows affected (0.02 sec)

mysql> SHOW TABLES;
+--------------------+
| Tables_in_students |
+--------------------+
| students           |
+--------------------+
1 row in set (0.00 sec)

```

---

## 8. 删除数据库

从删库到跑路

```sql

DROP DATABASE students

```
















# 参考：


1. http://www.splaybow.com/post/mysql-describe-view-table-structure.html

2. https://blog.51cto.com/xpleaf/1712821

3. https://www.liaoxuefeng.com/wiki/1177760294764384/1179611020917408


















