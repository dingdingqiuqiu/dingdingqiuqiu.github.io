---
title: SQL多表查询
mathjax: true
date: 2024-10-13 19:23:00
tags:
categories:
---

本文主要记录数据库实验作业的完成过程，供期末复习使用。

<!--more-->

在这之前

https://blog.csdn.net/weixin_44867329/article/details/137777665

## 数据库实验三 数据查询二

https://blog.csdn.net/weixin_66547608/article/details/131141175

### 第一题：

统计每本书借阅的次数,显示书名和借阅次数（借阅次数命名为`jycs`),按借阅次数降序排列,借阅次数相同的按书名降序排列。

错误

```sql
SELECT book.sm,count(borrow.jyrq)as jycs FROM book,borrow GROUP BY sm ORDER BY jycs desc,book.sm desc;
```

`group by`用法：

正确

```sql
select sm,count(sm)as jycs from borrow left join book on book.txm=borrow.txm group by sm order by jycs desc,sm desc;
```



### 第二题

统计借阅次数在2次以上的图书的借阅的次数,显示书名和借阅次数，按借阅次数降序排列,借阅次数相同的按书名降序排列

```sql
select sm,count(sm) as jycs from borrow left join book on book.txm=borrow.txm group by sm having(jycs>=2) order by jycs desc,sm desc;
```

正确：

```sql
select sm,count(sm) as jycs from borrow left join book on book.txm=borrow.txm GROUP BY sm HAVING(jycs>=2) ORDER BY jysc desc,sm desc;
```



### 第三题

统计每个出版社的图书的借阅次数,显示出版社的名称和借阅次数，按借阅次数降序排列,借阅次数相同的按出版社降序排列

```sql
select cbs,count(cbs) as jycs 
from borrow left join book on book.txm=borrow.txm 
group by cbs
order by jycs desc,cbs desc;
```

### 第四题

统计每位读者借阅的次数,显示姓名和借阅次数,按借阅次数降序排列，借阅次数相同的按姓名降序排列

```sql
select xm,count(xm) as jycs from borrow left join reader on borrow.dzzh=reader.dzzh 
group by xm
order by jycs desc,xm desc;
```

### 第五题

统计研究生读者借阅的次数,显示姓名和借阅次数，按借阅次数降序排列，借阅次数相同的按姓名降序排列

```sql
select xm,count(xm) as jycs from borrow left join reader on borrow.dzzh=reader.dzzh where sf='研究生'group by xm order by jycs desc,xm desc;开始你的任务吧，祝你成功！

use library;
#代码开始
#答案1
select sm from book where cbs="上海古籍出版社" and sm!="李白全集";
#答案2
select sm,sj from book where sj>(select avg(sj) from book);
#答案2
select txm,sm,sj from book where sj=(select max(sj) from book);
 
#答案3
select txm,sm,sj from book where sj=(select min(sj) from book);
 
 
 #代码结束
任务描述
本关任务： 第一题 查询曾经借过图书的读者的读者证号和姓名 第二题 查询曾经没有被借阅的图书的条形码和书名 第三题 查询与孙思旺借过相同图书的读者的读者证号和姓名，按读者证号升序排列 第四题 查询借阅过李白全集的读者所借过的其他图书的书名 按书名升序排列

开始你的任务吧，祝你成功！

 use library;
#代码开始
#题目1
select dzzh,xm from reader where reader.dzzh in (select dzzh from borrow);
#题目2
select txm,sm from book where book.txm not in (select txm from borrow);
 
#题目3
select dzzh,xm from reader where reader.dzzh in (select dzzh from borrow 
where txm in (select txm from borrow 
where borrow.dzzh=(select dzzh from reader 
where xm='孙思旺'))) 
and dzzh!='006' order by dzzh asc;
 
 
#题目4
 select sm from book where book.txm in (select txm from borrow
where borrow.dzzh in (select dzzh from borrow 
where borrow.txm=(select txm from book where sm="李白全集")))
and sm!="李白全集" order by sm asc;
 
 #代码结束

```

#### 任务描述

本关任务：根据图书数据表进行子查询

#### 相关知识

为了完成本关任务，你需要掌握：子查询

子查询

在SELECT语句中，一个查询语句完全嵌套在另一个查询语句的WHERE或HAVING的条件短语中，称为子查询或嵌套查询。 通常把条件短语中的查询成为子查询，父查询则使用子查询的查询结果作为查询条件。

#### 任务要求

第一题 查询与李白全集同一个出版社的图书的书名（不包括李白全集） 第二题 查询高于图书的平均售价(sj)的图书的书名和售价 第三题 查询售价最高的图书的条形码、书名和售价 第四题 查询售价最低的图书的条形码、书名和售价

------

开始你的任务吧，祝你成功！

```csharp
use library;



#代码开始



#答案1



select sm from book where cbs="上海古籍出版社" and sm!="李白全集";



#答案2



select sm,sj from book where sj>(select avg(sj) from book);



#答案2



select txm,sm,sj from book where sj=(select max(sj) from book);



 



#答案3



select txm,sm,sj from book where sj=(select min(sj) from book);



 



 



 #代码结束
```

#### 任务描述

本关任务： 第一题 查询曾经借过图书的读者的读者证号和姓名 第二题 查询曾经没有被借阅的图书的条形码和书名 第三题 查询与孙思旺借过相同图书的读者的读者证号和姓名，按读者证号升序排列 第四题 查询借阅过李白全集的读者所借过的其他图书的书名 按书名升序排列

开始你的任务吧，祝你成功！

```csharp
 use library;



#代码开始



#题目1



select dzzh,xm from reader where reader.dzzh in (select dzzh from borrow);



#题目2



select txm,sm from book where book.txm not in (select txm from borrow);



 



#题目3



select dzzh,xm from reader where reader.dzzh in (select dzzh from borrow 



where txm in (select txm from borrow 



where borrow.dzzh=(select dzzh from reader 



where xm='孙思旺'))) 



and dzzh!='006' order by dzzh asc;



 



 



#题目4



 select sm from book where book.txm in (select txm from borrow



where borrow.dzzh in (select dzzh from borrow 



where borrow.txm=(select txm from book where sm="李白全集")))



and sm!="李白全集" order by sm asc;



 



 #代码结束
```

## **MySQL数据库 - 连接查询**

https://blog.csdn.net/weixin_44867329/article/details/139355889

任务描述
本关任务：使用内连接查询数据表中学生姓名和对应的班级。

相关知识
为了完成本关任务，你需要掌握：
1.什么是内连接查询； 
2.如何使用内连接查询。

内连接查询
仅将两个表中满足连接条件的行组合起来作为结果集，称为内连接；

关键字：[inner] join ...  on。

语法：

表1 [inner] join 表2 on 表1.字段=表2.字段
语法解释：

从表1中取出每一条记录，去表2中与所有的记录进行匹配，匹配必须是某个条件在表1中与表2中相同，最终才会保留结果，否则不保留。inner 关键字可省略不写；on 表示连接条件：条件字段就是代表相同的业务含义（如下面两张表中的 employee.dept_id 和 department.id），大多数情况下为两张表中的主外键关系。
内连接查询的使用
现在我们有两张表，数据如下：
employee表数据：

id	name	dept_id
1	Nancy	4
2	Tod	2
3	Carly	1
4	Allen	2
5	Mary	(null)
department表数据：

id	name
1	开发部
2	测试部
3	运维部
4	销售部
现在想要查询出员工姓名以及其对应的部门名称，我们就使用内连接来进行查询。

我们可以将关联查询思路分为三步：
1.确定所连接的表，
2.确定所要查询的字段，
3.确定连接条件与连接方式。



其中，没有部门的员工和部门没有员工的部门都没有被查出来，这就是内连接的特点，只查询在连接表中有对应的记录，其中dept.id=emp.dept_id是连接条件。

编程要求
在右侧编辑器补充代码，查询数据表中学生姓名以及对应的班级名称，将其对应的列名分别另命名为studentName和className。

我们为你提供了两张表，内容如下：

tb_student表数据：

id	name	class_id
1	Emma	2
2	Mary	4
3	Allen	(null)
4	Kevin	1
5	Rose	2
6	James	1
tb_class表数据：

id	name
1	软件1631
2	软件1632
3	测试1631
4	测试1632
测试说明
平台会对你编写的代码进行测试：

预期输出：

 studentName    className
    Kevin       软件1631
    James       软件1631
    Emma        软件1632
    Rose        软件1632
    Mary        测试1632
开始你的任务吧，祝你成功！

第一关

```sql

USE School;
 
########## 查询数据表中学生姓名和对应的班级 ##########
#请在此处添加实现代码
########## Begin ##########
select tb_student.name as studentName,tb_class.name as className 
from tb_student inner join tb_class 
on tb_student.class_id=tb_class.id
 
 
########## End ##########

```

第二关：

```sql

USE School;
 
########## 使用左外连接查询所有学生姓名和对应的班级 ##########
 
#请在此处添加实现代码
########## Begin ##########
 #studentName列在左，className列在右
select tb_student.name as studentName,tb_class.name as className
from tb_class right join tb_student #class表右插入student表，student表在左边，以在左边的为name（emma-mary-allen-kevin-rose-james）为优先顺序先排（先放），
#然后将class表根据id号相等后连接
on tb_class.id=tb_student.class_id;
 
 
########## End ##########
 
########## 使用右外连接查询所有学生姓名和对应的班级 ##########
 
#请在此处添加实现代码
########## Begin ##########
#studentName列在左，className列在右
select tb_student.name as studentName,tb_class.name as className 
from tb_class left join tb_student#class表左插入student表，class表在左边，以在左边表的name（1631-1632-1631-1632）为顺序，根据class与student表的id号相等，连接
on tb_class.id=tb_student.class_id;
 
 
########## End ##########

```

第三关

```sql
USE School;
 
########## 查询所有班级里分数在90分以上的学生的姓名和学生的成绩以及学生所在的班级 ##########
#请在此处添加实现代码
########## Begin ##########
select s1.name as studentName,score,s2.name as className 
from tb_student as s1,tb_class as s2 
where s1.class_id=s2.id and s1.score>90 
order by score desc ;

```

第四关

```sql

USE School;
 
#请在此处添加实现代码
########## Begin ##########
 
#1.查询表中2,3,4年级中分别男女的总人数
select gradeId,sex,count(*) from student 
where gradeid in (2,3,4) group by gradeid,sex;
 
-- 方法2
select gradeId,sex,count(*) from student 
group by gradeid,sex having gradeid in (2,3,4);


```

