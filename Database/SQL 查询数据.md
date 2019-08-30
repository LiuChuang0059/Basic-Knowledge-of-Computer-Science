本文为 廖雪峰老师 SQL 教程学习笔记

文中示例数据库表的创建参照上一篇笔记



---

## 1. 查询数据


### 1. 基本查询

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


select  是关键字， 表示进行一个查询，

“ * ” 表示所有列

from 表示： 从哪个数据库表进行查询

----

### 2. 条件查询

通过 WHERE 条件来设置查询的条件

```sql
mysql> SELECT * FROM student_test WHERE score > 90;
+----+------+-------+--------+
| id | name | score | gender |
+----+------+-------+--------+
| 2  | zou  |    95 | female |
+----+------+-------+--------+
1 row in set (0.01 sec)

```

* WHERE 后面的条件表达式， 可以使用 条件运算 AND ，OR， NOT
> 要组合三个 以上的条件，就需要加入小括号调整优先级，默认的优先级是 ， NOT， 其次是 AND ， 最后是 OR


```sql
mysql> SELECT * FROM student_test WHERE score > 80 and gender = "male";
+----+------+-------+--------+
| id | name | score | gender |
+----+------+-------+--------+
| 1  | liu  |    90 | male   |
+----+------+-------+--------+
1 row in set (0.00 sec)

```

----

### 3. 投影查询

条件查询可以筛选出我们想要的记录， 但是返回的是和原表结构相同的 二维表， 有时候我们只想要返回某些列的数据。


只保留  id score ， name 三列
```sql

mysql> SELECT id, score, name FROM student_test;
+----+-------+------+
| id | score | name |
+----+-------+------+
| 1  |    90 | liu  |
| 2  |    95 | zou  |
+----+-------+------+
2 rows in set (0.00 sec)

```



1. 注意到，返回的表的列的顺序因为我们查询的顺序和原表不一样


2. 可以给每个列起一个 别名

```sql
mysql> SELECT id, score points, name FROM student_test;
+----+--------+------+
| id | points | name |
+----+--------+------+
| 1  |     90 | liu  |
| 2  |     95 | zou  |
+----+--------+------+
2 rows in set (0.00 sec)
```

score 的列名 重命名为 points， 原 数据库表的列名不会改变


```sql
mysql> select * from student_test;
+----+------+-------+--------+
| id | name | score | gender |
+----+------+-------+--------+
| 1  | liu  |    90 | male   |
| 2  | zou  |    95 | female |
+----+------+-------+--------+
2 rows in set (0.00 sec)
```


3. 复合  WHERE  条件

```sql

mysql> SELECT id, score points, name FROM student_test WHERE gender = "male";
+----+--------+------+
| id | points | name |
+----+--------+------+
| 1  |     90 | liu  |
+----+--------+------+
1 row in set (0.01 sec)

```

-----

### 4. 排序
使用 SELECT 查询的时候，返回的结果设计按照 id 排序的（根据主键排序的）

1. 可以选择其他的条件进行排序

```sql

mysql> SELECT id, name ,score FROM student_test ORDER BY score DESC;
+----+------+-------+
| id | name | score |
+----+------+-------+
| 2  | zou  |    95 |
| 1  | liu  |    90 |
+----+------+-------+
2 rows in set (0.00 sec)

```

* order by 关键词 加排序条件

* DESC 表示 倒序


2. 可以添加多个 排序条件， 先按第一个拍， 相同的再按着第二个排

```sql
mysql> SELECT id, name ,score FROM student_test ORDER BY score DESC ,gender;
```



3. 复合 WHERE 语句

```sql
mysql> SELECT id, name ,score FROM student_test WHERE gender = "male" ORDER BY score DESC;
+----+------+-------+
| id | name | score |
+----+------+-------+
| 1  | liu  |    90 |
+----+------+-------+
1 row in set (0.00 sec)

```

-----

### 5. 分页查询

SELECT 查询的时候， 结果数据量很大，放在一个页面显示不下，需要分页显示

```sql

mysql> SELECT id, name ,score FROM student_test ORDER BY score DESC LIMIT 2 OFFSET 0 ;
+----+------+-------+
| id | name | score |
+----+------+-------+
| 2  | zou  |    95 |
| 1  | liu  |    90 |
+----+------+-------+
2 rows in set (0.01 sec)

mysql> SELECT id, name ,score FROM student_test ORDER BY score DESC LIMIT 1 OFFSET 0 ;
+----+------+-------+
| id | name | score |
+----+------+-------+
| 2  | zou  |    95 |
+----+------+-------+
1 row in set (0.00 sec)

mysql> SELECT id, name ,score FROM student_test ORDER BY score DESC LIMIT 1 OFFSET 1 ;
+----+------+-------+
| id | name | score |
+----+------+-------+
| 1  | liu  |    90 |
+----+------+-------+
1 row in set (0.00 sec)

```

 LIMIT 1 OFFSET 0  : 每页有一条记录， 显示 第一页的记录



---

### 6. 聚合查询

使用 聚合函数， 统计总数，平均数 计算

1. COUNT() 计算 一共有多少记录

```sql

mysql> SELECT COUNT(*) FROM student_test;
+----------+
| COUNT(*) |
+----------+
|        2 |
+----------+
1 row in set (0.00 sec)


```

COUNT(* )  计算所有列的行数， 计算结果是个 数字，但是结果是 二维表， 为了方便以后处理， 可以给这个二维表设置一个别名

```sql
mysql> SELECT COUNT(*) num  FROM student_test;
+-----+
| num |
+-----+
|   2 |
+-----+
1 row in set (0.00 sec)

```

COUNT(* ) 等价于 COUNT(id)


2. 复合 WHERE 进行统计

统计 分数高于 90 的人数


```sql
mysql> SELECT COUNT(*) num90  FROM student_test WHERE score > 90 ;
+-------+
| num90 |
+-------+
|     1 |
+-------+
1 row in set (0.00 sec)
```


3. 其他的常用 聚合函数

* SUM ： 计算某一列的和 -- 数值类型的数据
* AVG ： 计算某一列的平均值-- 数值类型
* MAX（MIN） ： 计算 某一列的 最大值 --- 字符类型也可以， 返回排序靠前和排序靠后的数据

```sql
mysql> SELECT AVG(score) average  FROM student_test  ;
+---------+
| average |
+---------+
| 92.5000 |
+---------+
1 row in set (0.00 sec)

```


4. 计算 总页数

```sql
mysql> SELECT CEILING(COUNT(*)/1) pagenum  FROM student_test;
+---------+
| pagenum |
+---------+
|       2 |
+---------+
1 row in set (0.00 sec)

```

5. 分组聚合

统计男生和女生的数量， 可以使用分组功能

```sql

mysql> SELECT COUNT(*) num FROM student_test GROUP BY gender;
+-----+
| num |
+-----+
|   1 |
|   1 |
+-----+
2 rows in set (0.01 sec)

```

返回 两个结果， 但是不知道分别属于哪一个类别

```sql
mysql> SELECT gender , COUNT(*) num FROM student_test GROUP BY gender;
+--------+-----+
| gender | num |
+--------+-----+
| male   |   1 |
| female |   1 |
+--------+-----+
2 rows in set (0.00 sec)

```

**按照多个列进行分组**

```sql
mysql> SELECT gender ,score, COUNT(*) num FROM student_test GROUP BY gender, score;
+--------+-------+-----+
| gender | score | num |
+--------+-------+-----+
| male   |    90 |   1 |
| female |    95 |   1 |
+--------+-------+-----+
2 rows in set (0.00 sec)
```
> 这里 数据库表建的有点简单， 分组聚合的效果不明显


----

### 7. 连接查询

**连接查询 对多个表进行 JOIN 运算， 先确定一个主表作为结果集，然后把其他的表的行有选择性的 连接在主表的结果集上面**

我们对上面的 student_test 表添加一行 新的列 class_id .

```sql

mysql> ALTER TABLE student_test ADD COLUMN class_id char(10) NOT NULL;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> DESC student_test;
+----------+----------+------+-----+---------+-------+
| Field    | Type     | Null | Key | Default | Extra |
+----------+----------+------+-----+---------+-------+
| id       | char(10) | NO   | PRI | NULL    |       |
| name     | char(20) | NO   |     | NULL    |       |
| score    | int(11)  | NO   |     | NULL    |       |
| gender   | char(6)  | NO   |     | NULL    |       |
| class_id | char(10) | NO   |     | NULL    |       |
+----------+----------+------+-----+---------+-------+
5 rows in set (0.00 sec)

mysql> SELECT * FROM student_test;
+----+------+-------+--------+----------+
| id | name | score | gender | class_id |
+----+------+-------+--------+----------+
| 1  | liu  |    90 | male   |          |
| 2  | zou  |    95 | female |          |
+----+------+-------+--------+----------+
2 rows in set (0.00 sec)

mysql> UPDATE student_test SET class_id = 1 WHERE id = 1;
Query OK, 1 row affected (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> UPDATE student_test SET class_id = 2 WHERE id = 2;
Query OK, 1 row affected (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 0

mysql> SELECT * FROM student_test;
+----+------+-------+--------+----------+
| id | name | score | gender | class_id |
+----+------+-------+--------+----------+
| 1  | liu  |    90 | male   | 1        |
| 2  | zou  |    95 | female | 2        |
+----+------+-------+--------+----------+
2 rows in set (0.00 sec)

```


除了上面的 student_test 表，我们又建了一个 class_test 的表

```sql
mysql> SELECT * FROM class_test;
+----+--------+
| id | name   |
+----+--------+
| 1  | class1 |
| 2  | class2 |
+----+--------+
2 rows in set (0.00 sec)

```


1. student 的表中只有 class id ，没有 class name， class name 储存在 class_test 表之中

使用 常用的 INNER JOIN  , **内连接实现**

![](https://i.loli.net/2019/07/14/5d2b25e3be3fb81056.png)

INNER JOIN 就是选出两个表都存在的记录





```sql
mysql> SELECT s.id ,s.name, s.score,s.gender, s.class_id, c.name class_name FROM student_test s
INNER JOIN class_test c ON s.class_id = c.id;

+----+------+-------+--------+----------+------------+
| id | name | score | gender | class_id | class_name |
+----+------+-------+--------+----------+------------+
| 1  | liu  |    90 | male   | 1        | class1     |
| 2  | zou  |    95 | female | 2        | class2     |
+----+------+-------+--------+----------+------------+
2 rows in set (0.00 sec)

```
* FROM student_test s  : 确定主表

* INNER JOIN class_test c ： 确定需要连接的表

* ON s.class_id = c.id ： 确定连接条件 ： s 表的 class_id 与 c 表的 id 列相同


----

































