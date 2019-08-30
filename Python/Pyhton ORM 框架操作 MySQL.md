

## 1. ORM 框架

1. ORM ： object relation mapping， 对象关系映射


2. 在程序层面操作数据， 将数据放到内存之中进行处理， 通过持久化层从数据库中提取数据， 也可以通过持久化层将数据储存到数据库之中。

3. ORM 做的映射是数据库层和 业务逻辑层之间的一个映射 ： 提供一个持久化的模式，高效访问数据库，专注到业务逻辑层面， 不需要太关注底层的数据库访问细节。

4. ORM 框架适配各种 DBMS ： MySQL， SQLite

5. python 中的 ORM 框架

* Django 框架： WEB应用开发框架 。 MTV 模式： 模型 model 视图 View 模版 Template

* SQLALchemy

* peewee ： 轻量级 ORM 框架

模型和数据库表之间的映射

![](https://i.loli.net/2019/07/23/5d3679a550bc499413.jpg)


----


## 2. ORM 操作

```python

from urllib import parse
from sqlalchemy import create_engine
engine = create_engine('mysql+mysqlconnector://root:%s@localhost:3306/students'% parse.unquote_plus('password'))

# 数据库+ 数据库连接框架 用户名 ： IP地址：端口号/数据库名称
# 默认端口号 3306

from sqlalchemy.ext.declarative import declarative_base
Base = declarative_base()

from sqlalchemy import Column,String, Integer, Float
# 导入数据库表中可能使用的数据类型

from sqlalchemy.orm import  sessionmaker
DBSession = sessionmaker(bind = engine)

```


```python

class Students(Base):

    __tablename__ = "students" #。表的名字
    __table_args__ = {'extend_existing': True}

    # 名字和 创建的表的字段名字保持一致
    id = Column(Integer, primary_key = True)
    sname = Column(String)
    score = Column(Integer)
    gender = Column(String(6))



class Hero(Base):

    __tablename__ = "hero"
    __table_args__ = {'extend_existing': True}
    id = Column(Integer, primary_key = True)
    name = Column(String(255))




```

* 注意， 数据库中要有相应的数据库表， 再在 ORM 中创建相应的模型（使用我们之前创建的 students 表）

```sql
mysql> DESC students;
+--------+----------+------+-----+---------+-------+
| Field  | Type     | Null | Key | Default | Extra |
+--------+----------+------+-----+---------+-------+
| id     | int(11)  | NO   | PRI | NULL    |       |
| sname  | char(10) | NO   |     | NULL    |       |
| score  | int(11)  | NO   |     | NULL    |       |
| gender | char(6)  | NO   |     | NULL    |       |
+--------+----------+------+-----+---------+-------+
4 rows in set (0.00 sec)


mysql> DESC hero;
+-------+--------------+------+-----+---------+-------+
| Field | Type         | Null | Key | Default | Extra |
+-------+--------------+------+-----+---------+-------+
| id    | int(11)      | NO   | PRI | NULL    |       |
| name  | varchar(255) | NO   |     | NULL    |       |
+-------+--------------+------+-----+---------+-------+
2 rows in set (0.01 sec)

```

* 名字和 创建的表的字段名字保持一致

* 多个表可以创建多个 class



添加一行记录

```python

session = DBSession() # 创建 session， 相当于数据库连接

hero = Hero(id = 23, name = "诸葛亮") # 创建 hero 对象
session.add(hero) # 添加到session
session.commit() # 提交
session.close()

```

添加结果

```sql
mysql> SELECT * FROM hero;
+----+-----------+
| id | name      |
+----+-----------+
|  1 | 刘备      |
|  2 | 关羽      |
|  3 | 张飞      |
|  4 | 吕布      |
| 22 | kobe      |
| 23 | 诸葛亮    |
+----+-----------+
6 rows in set (0.00 sec)


```


查询数据


```python
session = DBSession()
student = session.query(Students).filter(Students.score ==90 ).one()
print("type : {}".format(type(student)))
print("name: {}".format(student.sname))
session.close()


##### 查询结果

type : <class '__main__.Students'>
name: liu

```


```python

session = DBSession()
student = session.query(Students).filter(Students.score >= 90 ).all()
for student in student:
    print("type : {}".format(type(student)))
    print("name: {}".format(student.sname))
    print("score: {}".format(student.score))
session.close()

```

筛选 分数高于90 的

```

type : <class '__main__.Students'>
name: liu
score: 90
type : <class '__main__.Students'>
name: li
score: 100
type : <class '__main__.Students'>
name: zou
score: 99
​
```



* query() 相当于 SELECT

* filter 相当于 WHERE


多条件查询：

```python

from sqlalchemy import  and_, or_
session = DBSession()
student = session.query(Students).filter(and_(Students.score >= 90 ,Students.score < 95 )).all()
for student in student:
    print("type : {}".format(type(student)))
    print("name: {}".format(student.sname))
    print("score: {}".format(student.score))
session.close()

```

大于等于 90 ， 小于 95 的 学生



删除数据：hero 数据表 混入一个 kobe， 我们进行删除


```python
row = session.query(Hero).filter(Hero.name == "kobe").first()
session.delete(row)
session.commit()
session.close()
```

```sql
mysql> SELECT * FROM hero;
+----+-----------+
| id | name      |
+----+-----------+
|  1 | 刘备      |
|  2 | 关羽      |
|  3 | 张飞      |
|  4 | 吕布      |
| 23 | 诸葛亮    |
+----+-----------+
5 rows in set (0.00 sec)

```

* 先查询，再从 session中 删除数据




修改数据 ：

```python
row = session.query(Hero).filter(Hero.name == "吕布").first()
row.name = "赵云"
session.commit()
session.close()

```


```sql
mysql> SELECT * FROM hero;
+----+-----------+
| id | name      |
+----+-----------+
|  1 | 刘备      |
|  2 | 关羽      |
|  3 | 张飞      |
|  4 | 赵云      |
| 23 | 诸葛亮    |
+----+-----------+
5 rows in set (0.00 sec)
```






# 参考

1. SQLalchemy连接数据库时密码包含特殊字符 ---https://www.jianshu.com/p/e4ccd32ce53b

2. SQLAlchemy inheritance not working  --- https://stackoverflow.com/questions/27812250/sqlalchemy-inheritance-not-working

3. 如何查看mysql 默认端口号和修改端口号 --- https://blog.csdn.net/ffggnfgf/article/details/50516269

4. 廖雪峰官方网站 ----- https://www.liaoxuefeng.com/wiki/1016959663602400/1017803857459008

5. 极客时间 ---- https://time.geekbang.org/column/article/109227


