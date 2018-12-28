---
title: SQLite 之 INSERT OR REPLACE使用
date: 2018-12-28 18:29:31
tags: [数据库,编程]
description: 
copyright: true
categories: 编程
top:
---
SQLite是一个C语言库，它实现了一个 小型， 快速， 自包含， 高可靠性， 功能齐全的 SQL数据库引擎。SQLite是世界上使用最多的数据库引擎。它内置于所有移动电话和大多数计算机中，并且捆绑在人们每天使用的无数其他应用程序中。下面是介绍如何使用`INSERT OR REPLACE`命令。

![SQLite](https://raw.githubusercontent.com/Duanruilong/phone_drl/master/image/blog/sqlite.png)

<!--more-->

# 业务需求
具体业务中,遇到需要批量插入和修改数据库的情况。
- 当前数据不存在，进行数据插入操作
- 当前数据存在，进行数据更新操作

以上的业务情况，可以使用SQlite的 `INSERT OR REPLACE`命令进行操作，具体的语法：
```h
INSERT OR REPLACE INTO table-name (column-name,...) VALUES (column-value,...)
```

# UNIQUE 约束 或 PRIMARY KEY约束

> UNIQUE约束: 唯一约束,防止在一个特定的列存在两个记录具有相同的值

> PRIMARY KEY约束: 约束唯一标识数据库表中的每条记录。

## 相同点

PRIMARY KEY也是UNIQUE,即不可重复

## 不同点

- 1.PRIMARY KEY是非空的,但是UNIQUE可以为空
- 2.只能有一个主键,但是唯一索引可以有多个
- 3.如果没有指定聚合索引,那么主键默认为聚合索引,唯一索引不具备该特性



```h
INSERT OR REPLACE INTO table-name (column-name,...) VALUES (column-value,...)
```
在如上的`colunm-name`中,如果已插入的`column-value`均无已存在的值,则判断插入的该条数据不存在,反正存在.




创建表：

```h
CREATE TABLE TARD ( ID INT PRIMARY KEY, name TEXT NOT NULL UNIQUE, age INT NOT NULL , date CHAR(50),Dvalue REAL DEFAULT 300.00);

```

```h

// 在xiaoming不存在的情况下进行INSERT

INSERT OR REPLACE INTO TARD (ID, name, age, date) VALUES (1, 'xiaoming', 18, '打电话')

// 结果

ID	name	    age	   date     Dvalue
1	xiaoming	18	   打电话	 300


// 在xiaoming已经存在的情况下进行update

INSERT OR REPLACE INTO TARD (ID, name, age, date) VALUES (1, 'xiaoming', 118, '去打电话')

// 结果

ID	 name	    age	   date       Dvalue
1	xiaoming	118	   去打电话	   300


```

创建表的时候`ID`和`name`都是`UNIQUE`的,命令只有在两个值都不存在时,才会进行插入,否则更新当前数据


## 注意

如果`INSERT OR REPLACE INTO table-name (column-name,...) VALUES (column-value,...)`该数据已存在,在更新时,某行数据没填写,则默认为空,覆盖之前的数据.

举例:

```h
INSERT OR REPLACE INTO TARD (ID, name, age, date) VALUES (1, 'xiaoming', 118)
```


xiaoming已在数据库中,会进行更新操作,没有填写`date`数值,则`date`数值不会被保留,而且替换为`NULL`


```h
ID	 name	    age	   date       Dvalue
1	xiaoming	118	   NULL	   300

```

