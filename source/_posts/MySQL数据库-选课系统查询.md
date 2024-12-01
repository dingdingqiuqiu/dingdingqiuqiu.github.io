---
title: MySQL数据库-选课系统查询
mathjax: true
date: 2024-11-17 19:57:32
tags:
categories:
---

选课系统查询

<!--more-->
### 第1关：数据库数据的插入

#### 应用

请仔细阅读右侧代码，根据方法内的提示，在Begin-End区域内进行代码补充，具体任务如下：

表结构已经为你创建好，只需往学生表（student）、课程表（course）和学生选课表（dbsc）中插入相应数据即可。

```sql
USE School;
 
#请在此添加实现代码
########## Begin ##########
########## 插入学生表（Student）相应数据 ##########
insert into student(Sno,Sname,Ssex,Sage,Sdept) values(9512101,'李勇','男',19,'计算机系'),
        (9512102,'刘晨','男',20, '计算机系'),
        (9512103,'王敏', '女',20,'计算机系'),
        (9521101,'张立','男',22,'信息系'),
        (9521102,'吴宾','女',21,'信息系'),
        (9521103,'张海','男',20,'信息系'),
        (9531101,'钱小平','女',18,'数学系'),
        (9531102,'王大力','男',19,'数学系');
########## 插入课程表（Course）相应数据 ##########
insert into course(Cno,Cname,Ccredit,Semster,Period) values('C01','计算机文化学',3,1,41),
        ('C02','VB',2,3,61),
        ('C03','计算机网络',4,7,14),
        ('C04','数据库基础',6,6,24),
        ('C05','高等数学',8,2,19),
        ('C06','数据结构',5,4,55);
########## 插入学生选课表（DBSC）相应数据 ##########
insert into dbsc(ScID,Sno,Cno,Grade,isTec) values(1,9512101,'c01',90,'必修'),(2,9512101,'c02',86,'选修'),(3,9512101,'c06',45,'必修'),
        (4,9512102,'c02',78,'选修'),(5,9512102,'c04',66,'必修'),
        (6,9521102,'c01',82,'选修'),(7,9521102,'c02',75,'选修'),(8,9521102,'c04',92,'必修'),(9,9521102,'c05',50,'必修'),
        (10,9521103,'c02',68,'选修'),(11,9521103,'c06',56,'必修'),
        (12,9531101,'c01',80,'选修'),(13,9531101,'c05',95,'必修'),
        (14,9531102,'c05',85,'必修');
########## End ##########
########## 查询表数据 ##########
SELECT * FROM student;
SELECT * FROM course;
SELECT * FROM dbsc;
```

### 第2关：简单查询

#### 应用

请仔细阅读右侧代码，根据方法内的提示，在Begin-End区域内进行代码补充，具体任务如下：

查询计算机系全体学生的姓名；

查询考试成绩不及格的学生的学号；

查询信息系年龄在20 ~ 23岁之间的学生的姓名以及其所在系和年龄；

查询选修修了课程C02的学生的学号以及其成绩，查询结果按成绩降序排列;

统计学生总人数。

```sql
#********* Begin *********#
echo "
select Sname, Sdept from student where Sdept = '计算机系';
 
select Sno from dbsc where Grade < 60;
 
select Sname, Sdept, Sage from student where Sage >=20 and Sage <=23 and Sdept = '信息系';
 
select Sno, Grade from dbsc where Cno = 'c02' order by Grade desc;
 
select count(*) from student;
 
"
#********* End *********#
 
```

### 第3关:进阶查询

#### 应用

代码补充，具体任务如下：

查询所有姓‘ 张 ’学生的详细信息；
查询信息系，数学系和计算机系学生的姓名和性别;
查询选修课程的人数，列出课程号和人数;
查询选修了3门课程以上的学生的学号;
查询计算机系学生的选课情况，要求列出学生的名字，所修课的课程号和成绩。
注意：编写查询语句时，需要查询列表所有信息时，请使用 表名.* （由于评测原因指定这样书写，实际自己使用可以直接*）。

```sql
#********* Begin *********#
echo "
select student.* from student where Sname like '张%';
 
select Sname, Ssex, Sdept from student where Sdept in ('计算机系','信息系','数学系');
 
select Cno, count(*) from dbsc where isTec= '选修' group by Cno;
 
select Sno from dbsc group by Sno having count(*) > 3;
 
select Sname,Cno,Grade from dbsc left join student on student.Sno=dbsc.Sno where student.Sdept='计算机系';
 
"
#********* End *********#
 
```

### 第4关:复杂查询

#### 应用

请仔细阅读右侧代码，根据方法内的提示，在Begin-End区域内进行代码补充，具体任务如下：

查询选了选修课程的学生，并列出学生的学号和姓名；
查询每名学生的选课门数和平均成绩，并列出相应信息；
查询选课门数等于或大于4门的学生的平均成绩和选课门数；
查询信息系选修VB课程的学生的成绩，要求列出学生姓名，课程名和成绩；
编写修改sql语句，将成绩小于60分的加5分。
注意：编写查询语句时，需要查询列表所有信息时，请使用 表名.* （由于评测原因指定这样书写，实际自己使用可以直接*）。

```sql
#********* Begin *********#
echo "
select distinct dbsc.Sno, student.Sname from dbsc join student on student.Sno=dbsc.Sno where dbsc.isTec = '选修';
 
select Sname, count(*), avg(Grade) from dbsc join student on student.Sno=dbsc.Sno  group by dbsc.Sno;
 
select avg(Grade),count(*) from dbsc join student on student.Sno=dbsc.Sno group by dbsc.Sno having count(*) >= 4;
 
select student.Sname, dbsc.Cno, dbsc.Grade from student left join dbsc on student.Sno=dbsc.Sno
where student.Sdept='信息系' and dbsc.isTec='选修' and Cno='C02';
 
update dbsc set Grade=Grade+5 where Grade < 60;
 
"
#********* End *********#
 
```

