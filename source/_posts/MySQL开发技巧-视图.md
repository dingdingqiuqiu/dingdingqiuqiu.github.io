---
title: MySQL开发技巧-视图
mathjax: true
date: 2024-10-29 17:03:44
tags:
categories: 数据库
---

本文基于头歌平台-MySQL开发技巧-视图学习相关知识，并做记录。

<!--more-->

### 第1关：视图

#### 创建视图

```SQL
CREATE [OR REPLACE] [ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}] VIEW view_name [(column_list)] AS select_statement [WITH [CASCADED | LOCAL] CHECK OPTION]
```

参数说明：

- OR REPLACE：表示替换已有视图；

- ALGORITHM：表示视图选择算法，默认算法是UNDEFINED(未定义的)： MySQL 自动选择要使用的算法 ；merge合并；temptable临时表；

- column_list：可选参数，指定视图中各个属性的名词，默认情况下与select语句中查询的属性相同；

- select_statement：表示select语句；

- [WITH [CASCADED | LOCAL] CHECK OPTION]：表示视图在更新时保证在视图的权限范围之内；cascade是默认值，表示更新视图的时候，要满足视图和表的相关条件；local表示更新视图的时候，要满足该视图定义的一个条件即可。

#### 创建视图示例

示例一：

![image-20241029172650778](https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VZMkVKQWM1RjhSUHFHblJ4dmlydjk4QmFDTkNfUHhhWXRrT1hIazB0UktWQ0E_ZT1wVVZkM3k.jpg)

示例二：

![image-20241029173015508](https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VlWmpvc0hkMk9KT2g1OTVjVmRZendnQllrRHpnNHFqSnZTbi1tTEF6WFJrX1E_ZT1OUXUyMU4.jpg)

以上两个示例可以看出，虽然两个视图的字段名不同，但是，数据是相同的，因为两个视图引用的是同一个表中的数据，并且，as后的创建视图的语句也相同。

在实际开发中，用户可以根据自己的需求，通过视图的方式，获取基本表中自己需要的数据，这样既能满足用户的需求，也不会破坏基本表原来的结构，从而保证了基本表中数据的安全性。

#### 操作视图

视图是逻辑表，也就是说视图不是真实的表，但操作视图和操作普通表的语法是一样的。

用户可以在视图中无条件地使用`select`语句查询数据。但使用`inser`、`update`和`delete`操作需要在创建视图时满足以下条件（满足以下条件的视图称为**可更新视图**）：

- from子句中只能引用有1个表（真实表或可更新视图）；

- 不能包含 with、distinct、group by、having、limit等子句；

- 不能使用复合查询，即不能使用union、intersect、except等集合操作；

- select子句的字段列表不能包含聚合、窗口函数、集合返回函数。

我们仍使用之前示例中的数据来操作视图：

![image-20241029174447708](https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VTWWEzSVMtWUN4UGlmYUoxTGJvZURjQjA3ZjBneEpsVmhucFBreWxmVjlYRHc_ZT1BTEFsY0g.jpg)

> 注意这里因为使用了limit子句，因此创建的视图是不可更新的，我们修改会失败。

#### 删除视图
若视图不再被需要，我们可以将其删除，且视图的删除并不影响源表中的数据。

删除视图的 SQL 如下：

```sql
DROP VIEW view_name;
```

![image-20241029174644473](https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VVZlk1bkpxc05aS3RFby03VkQzZ0hBQlZ1aGtKbnBsTkwwSEZvT0J4YmtWbVE_ZT1MenBaS0Y.jpg)

#### 应用

![image-20241029174720465](https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VYWWZOclppS1Q1SHJ2WmIyOVFXTWlvQkhUTkstcGhQSkdXMWU2dFhWT29weEE_ZT1oTVExNGQ.jpg)

```sql
use School;

#1.创建单表视图
CREATE VIEW stu_view AS 
SELECT 
    math, 
    chinese, 
    (math + chinese) AS "math+chinese" 
FROM 
    student;


#2.创建多表视图
CREATE VIEW stu_classes AS 
SELECT 
    student.stu_id, 
    student.name, 
    stu_info.classes
FROM 
    student 
JOIN 
    stu_info 
ON 
    student.stu_id = stu_info.stu_id;

```

> 注意创建单表视图时，定义一个`math+chese`之和字段的语句为`(math+chese) as "math+chinese" `注意这里的别名**需要用引号括起来**，不然包报错的。
>
> 行之和直接加，列之和`SUM`函数。 

> 创建多表视图时，连接操作是`JOIN 表名 ON 连接条件`