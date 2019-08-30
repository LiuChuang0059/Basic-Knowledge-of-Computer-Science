## 0. Python 数据库 API 规范

各种数据库提供统一的访问接口， 在切换数据库的时候， Python 层的代码改动较小

![](https://i.loli.net/2019/07/20/5d32c4bf3fad111251.png)


1. 引入 API 模块

2. 和数据库建立连接

3. 执行 SQL 语句

4. 关闭数据库连接







## 1. 安装 MySQL 驱动

官方提供的 驱动器

```
pip install mysql-connector-python
```







## 2. 连接到数据库

1. 操作数据库表， 需要先连接数据库， 数据库的连接称为 connection

```python

import mysql.connector
conn = mysql.connector.connect(user="root",password = "****",database = "students")
```

> 密码更换成自己的数据库密码, database 跟换成自己创建的数据库名

* 指定 host user， password，port 等参数创建数据库连接







2. 连接到数据库后，需要打开游标，称之为Cursor，。

```python
cursor = conn.cursor()

```


3. 通过Cursor执行SQL语句，然后，获得执行结果

>  创建一个数据 名字 user 表
```python
cursor.execute('create table user (id varchar(20) primary key, name varchar(20))')
```
* SQL 语句和 之前 SQL 相关文章中介绍的基本相同


```python

cursor.fetchall()   # 读取数据集中的所有行， 返回一个元组 类型
cursor.fetchone()  # 读取数据集中的一条数据
cursor.fetchmany(n)  #  读取数据集中的多条数据， 返回一个元组 类型



```

varchar ：

> char类型是使用固定长度空间进行存储，范围0-255。比如CHAR(30）能放30个字节，存放abcd时，尾部会以空格补齐，实际占用空间 30bytes 。检索它的时候尾部空格会被去除。char善于存储经常改变的值，或者长度相对固定的值，比如type、ip地址或md5之类的数据，不容易产生碎片。关于它的效率可以参考这里。

> varchar类型保存可变长度字符串，范围0-65535（但受到单行最大64kb的限制）。比如用varchar(30)去存放abcd，实际使用5个字节，因为还需要使用额外1个字节来标识字串长度（0-255使用1个字节，超过255需要2个字节）。varchar善于存储值的长短不一的列，也是用的最多的一种类型，节省磁盘空间。update时varchar列时，如果新数据比原数据大，数据库需要重新开辟空间，这一点会有性能略有损耗，但innodb引擎下查询效率比char高一点。这也是innodb官方推荐的类型。


4. 插入一行记录， 使用 %s 作为占位符

```python
cursor.execute('insert into user (id, name) values (%s, %s)', ['1', 'Michael'])
```


5. 操作之后， 使用 commit 提交

```python
conn.commit()
cursor.close()


```


6.  完整插入数据的操作


```python
import mysql.connector
conn = mysql.connector.connect(user="root",password = "@52zxw1314",database = "students")

cursor = conn.cursor()
cursor.execute("SELECT * FROM students")
cursor.fetchall()
# 查看现有的 表的数据

####

[(1, 'liu', 100, 'male'), (2, 'lin', 80, 'male'), (4, 'li', 100, 'male')]

cursor.execute("DESC students")
cursor.fetchall()
##### 查看现有表的字段#####
[('id', 'int(11)', 'NO', 'PRI', None, ''),
 ('sname', 'char(10)', 'NO', '', None, ''),
 ('score', 'int(11)', 'NO', '', None, ''),
 ('gender', 'char(6)', 'NO', '', None, '')]


 # 新增一个人

sql = "INSERT INTO students(id,sname,score,gender) VALUES(%s, %s,%s,%s)"
## 统一使用 s% 作为占位符

val = (5,"zou",99,"female")
cursor.execute(sql,val)

# 提交更改
conn.commit()

## 查询更改后的数据库表

cursor.execute("SELECT * FROM students")
data = cursor.fetchall()
for student in data:
    print(student)

##### 查询结果

(1, 'liu', 100, 'male')
(2, 'lin', 80, 'male')
(4, 'li', 100, 'male')
(5, 'zou', 99, 'female')

### 按照我们的想法 增加了一个数据

```

7. 修改 数据库表的数据

```python
sql = "UPDATE students SET score = %s WHERE sname = %s"
val = (90,"liu")
cursor.execute(sql,val)

conn.commit()
cursor.execute("SELECT * FROM students")
data = cursor.fetchall()
for student in data:
    print(student)


### 修改结果
(1, 'liu', 90, 'male')
(2, 'lin', 80, 'male')
(4, 'li', 100, 'male')
(5, 'zou', 99, 'female')

```


8. 执行完了之后，关闭数据库的连接

```
cursor.close()
conn.close()

```


---

## 3. 数据库操作异常检查


```python

import traceback
try:
    sql = "INSERT INTO students(id,sname,score,gender) VALUES(%s, %s,%s,%s)"
    val = (6,"wen",89,"female")
    cursor.execute(sql,val)
    conn.commit()
    print(cursor.rowcount," 插入 成功")
except Exception as e:
    traceback.print_exc()
    conn.rollback()
finally:
    conn.close()

#######

1  插入 成功

#####

conn = mysql.connector.connect(user="root",password = "@52zxw1314",database = "students")
cursor = conn.cursor()
cursor.execute("SELECT * FROM students")
data = cursor.fetchall()
for student in data:
    print(student)

###########

(1, 'liu', 90, 'male')
(2, 'lin', 80, 'male')
(4, 'li', 100, 'male')
(5, 'zou', 99, 'female')
(6, 'wen', 89, 'female')


```


----

# 参考

1. https://www.liaoxuefeng.com/wiki/1016959663602400/1017802264972000

2. https://segmentfault.com/a/1190000005124235
