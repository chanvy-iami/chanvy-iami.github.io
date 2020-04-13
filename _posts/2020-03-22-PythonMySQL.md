---
layout: post
title: Python MySQL
subtitle: mysql connector 驱动
date: 2020-03-22
author: Weiami
header-img: img/post-bg-2017.jpg
catalog: true
tags:
    - python
    - mysql
---

## 准备工作

1.管理员身份运行命令提示符，python环境下，安装mysql-connector驱动来使用MySQL。

* pip3 install mysql-connector 安装MySQL官方驱动
* import mysql.connector 验证安装是否成功

2.MySQL 8.0以上版本的密码插件验证方式发生变化。

* [mysqld] #首先修改my.ini配置
* default_authentication_plugin=mysql_native_password

* 启动MySQL服务，在mysql下进行以下操作
* ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '新密码'；

## 创建数据库连接 

```
import mysql.connector
mydb = mysql.connector.connect(
    host='localhost',				
    user='root',
    passwd='新密码'
)
print(mydb)
```

### 创建数据库

```
mycursor = mydb.cursor() # cursor()方法创建一个游标对象
mycursor.execute("CREATE DATABASE chenwei_db")
```

查看数据库是否创建成功。

`mycursor.execute("SHOW DATABASES")`

## 创建数据表

首先使用刚刚创建的数据库。

`mycursor.execute("USE chenwei_db")`

然后创建数据表。

`mycursor.execute("CREATE TABLE sites(name VARCHAR(255), url VARCHAR(255))")`

查看数据表是否创建成功。

```
mycursor.execute("SHOW TABLES")
for x in mycursor:
  print(x)
```

### 主键设置

使用“INT AUTO_INCREMENT PRIMARY KEY”设置主键，起始值为1，逐步递增,如果已经创建表，需要使用ALTER TABLE来给表添加主键。

`mycursor.execute("ALTER TABLE sites ADD COLUMN id INT AUTO_INCREMENT PRIMARY KEY")`

未创建表可直接给表设置主键

`mycursor.execute("CREATE TABLE sites (id INT AUTO_INCREMENT PRIMARY KEY, name VARCHAR(255) url VARCHAR(255))")`

## 插入数据

使用“INSERT INTO”插入数据。

```
sql = "INSERT INTO sites (name, url) VALUES (%s, %s)"
val = ("chenwei", "http://chenwei.com")
mycursor.execute(sql, val)
mydb.commit() # 数据表有更新，必须使用该语句
print(mycursor.rowcount, "记录插入成功。")
```

在数据插入后，可获取其ID。

`print("1 条记录插入成功， ID:"，mycursor.lastrowid)`

### 批量插入

使用executemany()方法批量插入，第二个参数为元组列表。

```
sql = "INSERT INTO sites (name, url) VALUES (%s, %s)"
val = [("taobao", "http://taobao.com"), ("baidu","http://baidu.com")]
mycursor.execute(sql, val)
mydb.commit() # 数据表有更新，必须使用该语句
print(mycursor.rowcount, "记录插入成功。")
```

## 查询数据

使用SELECT语句查询数据。

```
mycursor.execute("SELECT * FROM sites")
myresult = mycursor.fetchall() # 获取所有记录
for x in myresult:
  print(x)
```

获取指定的字段数据。

```
mycursor.execute("SELECT name FROM sites")
myresult = mycursor.fetchall()
for x in myresult:
  print(x)
```

只想获取一条数据。

```
mycursor.execute("SELECT * FROM sites")
myresult = mycursor.fetchone()
for x in myresult:
  print(x)
```

### where条件语句

使用WHERE语句获取指定条件的数据。

```
sql = "SELECT * FROM sites WHERE name ='taobao'"
mycursor.execute(sql)
myresult = mycursor.fetchall()
for x in myresult:
  print(x)
```

**注**：在此阶段执行代码过程中，会出现错误。经过求助发现问题，查询时，MySQLCursorBuffered游标会从服务器获取整个结果集并将其放入缓冲区。Buffered游标适用于多个小结果集的查询，且多个结果集之间的数据需一起使用。buffered游标执行查询语句时，取行方法返回缓冲区的行；而nonbuffered游标不从服务器获取数据，直到调用获取数据行的方法，nonbuffered游标必须确保取出的结果是结果集中的所有行，才可以执行其他语句，否则报错mysql.connector.errors.InternalError: Unread result found。

**解决方法**：重新创建数据库连接。

```
mydb = mysql.connector.connect(
    host='localhost',
    user='root',
    passwd='新密码',
    buffered=True
)
mycursor = mydb.cursor(buffered = True)
```

再次执行以上语句。

```
mycursor.execute("USE chenwei_db")
mycursor.execute("SHOW TABLES")
sql = "SELECT * FROM sites WHERE name ='taobao'"
mycursor.execute(sql)
myresult = mycursor.fetchall()
for x in myresult:
  print(x)
```

使用通配符%。

```
sql = "SELECT * FROM sites WHERE name LIKE '%bai%'"
mycursor.execute(sql)
myresult = mycursor.fetchall()
for x in myresult:
  print(x)
```

为防止数据库查询发生SQL注入的攻击，使用%s占位符来转义查询语句条件。

```
sql = "SELECT * FROM sites WHERE name=%s"
na = ("taobao",)
mycursor.execute(sql, na)
myresult = mycursor.fetchall()
for x in myresult:
  print(x)
```

### 排序

使用ORDER BY语句对查询结果进行排序。默认排序为升序，降序使用关键字DESC。

```
sql = "SELECT * FROM sites ORDER BY name"
myresult = mycursor.fetchall()
for x in myresult:
  print(x)
```

```
sql = "SELECT * FROM sites ORDER BY name DESC"
myresult = mycursor.fetchall()
for x in myresult:
  print(x)
```

### LIMIT 

设置查询的数据量，可通过LIMIT语句。

```
mycursor.execute("SELECT * FROM sites LIMIT 2")
myresult = mycursor.fetchall()
for x in myresult:
  print(x)
```

指定起始位置，使用关键字OFFSET。

```
mycursor.execute("SELECT * FROM sites LIMIT 2 OFFSET 1")
myresult = mycursor.fetchall()
for x in myresult:
  print(x)
```

## 删除记录

删除记录使用DELETE FROM语句。

```
sql = "DELETE FROM sites WHERE name='chenwei'"
mycursor.execute(sql)
mydb.commit()
print(mycursor.rowcount, "条记录删除")
```

为防止数据库查询发生SQL注入的攻击，使用%s占位符来转义删除语句条件。

```
sql = "DELETE FROM sites WHERE name=%s"
na = ("chenwei",)
mycursor.execute(sql,na)
mydb.commit()
print(mycursor.rowcount, "条记录删除")
```

## 更新表数据

使用UPDATE语句更新数据表。

```
sql = "UPDATE sites SET name ='tb' WHERE name ='taobao'"
mycursor.execute(sql)
mycursor.commit()
print(mycursor.rowcount, "条记录被修改")
```

为防止数据库查询发生SQL注入的攻击，使用%s占位符来转义更新语句条件。

```
sql = "UPDATE sites SET name =%s WHERE name =%s"
val = ('tb', 'taobao')
mycursor.execute(sql,val)
mycursor.commit()
print(mycursor.rowcount, "条记录被修改")
```

## 删除表

使用DROP TABLE语句删除表，IF EXISTS判断表是否存在。

```
sql = "DROP TABLE IF EXISTS sites"
mycursor.execute(sql)
```