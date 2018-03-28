---
layout: post
title: "MySQL UNION用法"
date: 2018-03-28
author: LiamHsia
category: Tutorial
finished: true
---

原文地址：[MySQL UNION](http://www.mysqltutorial.org/sql-union-mysql.aspx)

## 前言
在此教程中，你将会学到如何用`UNION`操作符来合并两个甚至多个`SELECT`的查询结果至一个结果集中。

## 用法
在MySQL数据库中，`UNION`操作符允许你合并多个查询结果集，下面是`UNION`操作符的语法：

```
SELECT column_list
UNION [DISTINCT | ALL]
SELECT column_list
UNION [DISTINCT | ALL]
SELECT column_list
```

想要使用`UNIOIN`合并多个查询结果集，下面是必须遵守的规则：

- `SELECT`查询的列的个数必须是相同的，比如：第一个查询有两列，第二个必须也是两列。
- 列的数据类型必须是相同的，或者是可以相互转换的。

默认情况下，`UNION`运算符会删除重复的数据，因为缺省默认是使用`DISTINCT`，但你可以使用`UNIOIN ALL`来解决这个问题。

好了，让我们看看下面的例子，表：`t1`和`t2`:

```
DROP TABLE IF EXISTS t1;
DROP TABLE IF EXISTS t2;
 
CREATE TABLE t1 (
    id INT PRIMARY KEY
);
 
CREATE TABLE t2 (
    id INT PRIMARY KEY
);
 
INSERT INTO t1 VALUES (1),(2),(3);
INSERT INTO t2 VALUES (2),(3),(4);
```

下面的`UNION`就会合并从`t1`和`t2`中取得的数据：

```
SELECT id
FROM t1
UNION
SELECT id
FROM t2;
```

但最终的结果是去重的：

```
+----+
| id |
+----+
|  1 |
|  2 |
|  3 |
|  4 |
+----+
4 rows in set (0.00 sec)
```

`UNIOIN`去掉了`2`,`3`，只会保留一个有效的值，诚如上面所说的，你可以加上`UNION ALL`来避免。

可能下面的韦恩图能更好的解释整个操作：

![](http://ooyc2y4k2.bkt.clouddn.com/2018-03-28-MySQL-UNION.png)

如果你使用`UNION ALL`，重复的数据便会保留下来，由于不处理重复数据，它的速度会比`UNIOIN DISTINCT`快很多。

```
SELECT id
FROM t1
UNION ALL
SELECT id
FROM t2;
```

```
+----+
| id |
+----+
|  1 |
|  2 |
|  3 |
|  2 |
|  3 |
|  4 |
+----+
6 rows in set (0.00 sec)
```

看到了吧，在使用`UNIOIN ALL`语句之后，重复的数据又重新回来了。

## UNIOIN vs. JOIN
同样的联合两张表的数据，那么`UNION`和`JOIN`又有什么区别呢？简单的说：

> `UNION`是垂直合并，而`JOIN`是水平合并

![](http://ooyc2y4k2.bkt.clouddn.com/2018-03-28-MySQL-UNION-vs-JOIN.png)

## 列别名
我们下面使用`customers`和`employees`这两张表来做演示，表结构如下：

![](http://ooyc2y4k2.bkt.clouddn.com/2018-03-28-employees_table.png)

![](http://ooyc2y4k2.bkt.clouddn.com/2018-03-28-customers_table.png)

假如你想要查询出雇员和顾客的名字，并且合并姓和名到一个结果集中，`UNIOIN`此时便可以很好的解决。

```
SELECT 
    firstName, 
    lastName
FROM
    employees 
UNION 
SELECT 
    contactFirstName, 
    contactLastName
FROM
    customers;
```

结果如下：

![](http://ooyc2y4k2.bkt.clouddn.com/2018-03-28-MySQL-UNION-example.png)

正如你所见，`UNION`会使用第一个`SELECT`语句的列名作为输出的名字，如果你想使用自己的别名，也很简单，在第一个`SELECT`语句中设置即可。

```
SELECT 
    concat(firstName,' ',lastName) fullname
FROM
    employees 
UNION SELECT 
    concat(contactFirstName,' ',contactLastName)
FROM
    customers;
```

新的结果：

![](http://ooyc2y4k2.bkt.clouddn.com/2018-03-28-MySQL-UNION-with-column-alias-example.png)

## 对结果排序
在最后一个`SELECT`语句之后，使用`ORDER BY`便可以对整个得到的结果集进行排序。

```
SELECT 
    concat(firstName,' ',lastName) fullname
FROM
    employees 
UNION SELECT 
    concat(contactFirstName,' ',contactLastName)
FROM
    customers
ORDER BY fullname;
```

![](http://ooyc2y4k2.bkt.clouddn.com/2018-03-28-MySQL-UNION-and-ORDER-BY-example.png)

需要我们注意的是，如果你在每一个`SELECT`语句之后都加上`ORDER BY`并不会影响最后一个`SELECT`语句之后的`ORDER BY`，所以说，只有最后一个才是有效的。

MySQL同样支持通过数据集所在位置排序的功能，如下使用`ORDER BY`即可：

```
SELECT 
    concat(firstName,' ',lastName) fullname
FROM
    employees 
UNION SELECT 
    concat(contactFirstName,' ',contactLastName)
FROM
    customers
ORDER BY 1;
```

通过这个教程，你一定学会和如何用`UNION`指令来合并多个查询语句的结果.

:)

