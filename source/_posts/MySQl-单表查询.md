---
title: MySQl-单表查询
mathjax: true
date: 2024-10-28 20:20:17
tags:
categories: 数据库
---

本文记录单表查询常用语句，依据头歌作业书写，对应MySql单表查询部分。

<!--more-->

## 单表查询（3）

### 第1关：

语法：

```sql
SELECT 字段名 FROM 表名 ORDER BY 字段名 [ASC[DESC]];
```

ASC 升序关键字
DESC 降序关键字

查询学生成绩表中1班同学的所有信息并以成绩降序的方式显示结果。

```SQL
########## 查询1班同学的所有信息以成绩降序的方式显示结果 ##########
SELECT * FROM tb_score WHERE CLASS_ID=1 ORDER BY score DESC;
```

### 第2关：分组查询

语法：

分组查询的关键字是Group By，查询的是每个分组中 首次出现的一条记录。

```sql
SELECT 字段名 FROM 表名 GROUP BY 字段名;
```

在右侧编辑器Begin-End处补充代码，对班级表中的班级名称进行分组查询。

```SQL
########## 对班级名称进行分组查询 ##########
SELECT * FROM tb_class GROUP BY CLASS_ID;
```

> 表名区分大小写

### 第3关：使用 LIMIT 限制查询结果的数量

LIMIT的使用
在我们查询大量数据结果时，会返回很多条数据，有需要的记录可能就其中的一条或者几条。比如，实现分页功能，若每页显示10条记录，每次查询就只需要10条记录。
在MySQL中，提供了LIMIT关键字，用来限制查询结果的数量。

语法：

```sql
SELECT 字段名 FROM 表名 LIMIT [OFFSET,] 记录数;
```

参数说明：

- 第一个参数，OFFSET，可选参数，表示偏移量，如果不指定默认值为0，表示从查询结果的第一条记录开始，若偏移量为1，则从查询结果中的第二条记录开始，以此类推。

- 第二个参数，记录数，表示返回查询结果的条数。

```sql
########## 查询班级中第2名到第5名的学生信息 ##########
SELECT * FROM tb_score ORDER BY score DESC LIMIT 1,4;
```

> 注意查询第2-5名学生时偏移量为1
