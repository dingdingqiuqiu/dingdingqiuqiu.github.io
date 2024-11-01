---
title: MySQL数据库-使用聚合函数查询
mathjax: true
date: 2024-10-29 15:35:23
tags:
categories: 数据库
---

本文主要基于MySQL数据库头歌作业讲解使用聚合函数查询。

<!--more-->

### 第1关：COUNT( )函数

#### COUNT函数用法

```sql
select count(*/字段名) from 数据表;
```

#### 应用

```sql
USE School;

#请在此处添加实现代码
########## Begin ##########

########## 查询该表中一共有多少条数据 ##########
SELECT COUNT(*) FROM tb_class;

########## 查询此表中367班有多少位学生 ##########
SELECT classid,COUNT(*) FROM tb_class WHERE classid=367;

########## End ##########
```

### 第2关：SUM( )函数

#### SUM()函数基本使用

```SQL
select sum(字段名) from 数据表;
```

#### 应用

```sql
USE School;

########## 查询所有学生总分数 ##########
SELECT sum(score) FROM tb_class;

########## 查询学生语文科目的总分数 ##########
SELECT course,sum(score) FROM tb_class WHERE course='语文';

```

### 第三关：AVG()函数

#### AVG()函数基本使用

```sql
select avg(字段名) from 数据表;
```

#### 应用

```sql
USE School;

########## 查询学生语文科目的平均分数 ##########
SELECT course,avg(score) FROM tb_class WHERE course='语文';

########## 查询学生英语科目的平均分数 ##########
SELECT course,avg(score) FROM tb_class WHERE course='英语';

```

### 第4关：MAX( )函数

#### MAX()函数基本使用
```sql
select max(字段名) from 数据表;
```

#### 应用

```sql
USE School;

########## 查询语文课程中的最高分数 ##########
SELECT course,max(score) FROM tb_class WHERE course='语文';

########## 查询英语课程中的最高分数 ##########
SELECT course,max(score) FROM tb_class WHERE course='英语';

```

### 第5关：MIN( )函数

#### MIN()函数基本使用

```sql
select min(字段名) from 数据表;
```

#### 应用

```sql
USE School;

########## 查询语文课程中的最高分数 ##########
SELECT course,min(score) FROM tb_class WHERE course='语文';

########## 查询英语课程中的最高分数 ##########
SELECT course,min(score) FROM tb_class WHERE course='英语';

```

