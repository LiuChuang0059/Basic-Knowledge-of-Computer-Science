## 1. 进入官网下载 最新社区版

https://dev.mysql.com/downloads/mysql/


![](https://i.loli.net/2019/07/13/5d2951d921b9b71957.png)

我选择的是 dmg 安装包


## 2. 安装指引安装，**中间会设置 root 的密码，记住，以后登陆要用**


## 3. 终端连接 SQL

* 在终端添加 MySQL 的路径

```
PATH="$PATH":/usr/local/mysql/bin
```

之后通过下面的命令登陆

```sql

mysql -u root -p

```

* -u 后面接的是用户名 ，默认是 root
* -p 后面接的是密码，

* 省略了 -h， 登陆当前主机的 MySQL 数据库可以省略,登陆远程主机的 需要加入远程主机的 IP地址


```

mysql -h 202.116.96.96 -u root -p
```

![](https://i.loli.net/2019/07/13/5d295344149c596808.png)


## 4. 检查 MySQL 编码

```sql
show variables like '%char%';

```
![](https://i.loli.net/2019/07/13/5d29557f6964d82823.png)

编码 utf8mb4 和 utf8 完全兼容，支持最新的 Unicode 标准， 可以显示 emoji




----


# 4. 参考

https://www.jianshu.com/p/07a9826898c0

https://www.liaoxuefeng.com/wiki/1177760294764384/1179611020917408