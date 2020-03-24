---
layout: post
title: Python MySQL
subtitle: PyMySQL驱动
date: 2020-03-23
aythor: Weiami
header-img: img/post-bg-2015.jpg
catalog: true
tags:
    - python
    - mysql
---

## 准备工作

Python3使用PyMySQL连接数据库。

1.安装PyMySQl

`pip3 innstall pymysql`

2.mysql环境下创建数据库chenwei_db

`CREATE DATABASE chenwei_db`

## 数据库连接

```
import pymysql
db = pymysql.connect(host='localhost',user='root',password='数据库密码',database='chenwei_db') # 打开数据库连接
cursor = db.cursor() # 创建游标对象
cursor.execute("SELECT VERSION()") #执行查询操作
data = cursor.fetchone() # 获取单条数据
print("Database version : %s" % data)
db.close() # 关闭数据库连接
```

## 创建数据库表

```
cursor.execute("DROP TABLE IF EXISTS employee")
sql = "CREATE TABLE employee (fn CHAR(20), ln CHAR(20), age INT, sex CHAR(1), income FLOAT)"
cursor.execute(sql)
db.close()
```

## 数据库插入操作

```
sql = "INSERT INTO employee(fn, ln, age, sex, income） VALUES ('wei', 'chen', 25, 'M', 2500)"
try:
    cursor.execute(sql)
    db.commit()
except:
    db.rollback()
db.close()
```

```
sql = "INSERT INTO employee(fn, ln, age, sex, income） VALUES ('%s', '%s', '%s', '%s', '%s')" %('w', 'c', 26, 'M', 2505)
try:
    cursor.execute(sql)
    db.commit()
except:
    db.rollback()
db.close()
```

## 数据库查询操作

* fetchall(): 获取下一个查询结果集，结果集是一个对象
* fetchone(): 接受全部的返回结果行
* rowcount(): 只读属性，执行execute()方法后影响的行数

```
sql = "SELECT * FROM employee WHERE income > %s" %(2500)
try:
    cursor.execute(sql)
    results = cursor.fetchall()
    for row in results:
	  f = row[0]
	  l = row[1]
	  a = row[2]
	  s = row[3]
	  i = row[4]
	  print(f, l, a, s, i)
except:
    print("Error: unable to fetch data")
```

## 数据库更新操作

```
sql = "UPDATE employee SET age = age+1 WHERE sex = '%c'" %('M')
try:
    cursor.execute(sql)
    db.commit()
except:
    db.rollback()
db.close()
```

## 删除操作

```
sql = "DELETE FROM employee WHERE age > %s" % (20)
try:
    cursor.execute(sql)
    db.commit()
except:
    db.rollback()
db.close()
```

## 执行事务

事务机制可以确保数据一致性。

* 原子性：一个事务是不可分割的，要么都做，要么都不做
* 一致性：事务必须是数据库从一个一致性状态到另一个
* 隔离性：一个事务不能被其他事务影响
* 持久性：事务一旦提交，对数据库的改变就是永久性的

## 错误处理

Error等其他数据库操作的错误及异常。

