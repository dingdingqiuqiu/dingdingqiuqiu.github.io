---
title: Mysql开发技巧-查询、索引和完整性
mathjax: true
date: 2024-11-17 19:22:02
tags:
categories:
---

本文基于头歌平台上实验MySql开发技巧-查询、索引和完整性。

<!--more-->

### 第1关：基本查询的学习

#### 遗漏的查询语句知识点



#### 应用

在右侧代码窗口区域的指定位置编写查询语句，实现对数据库YGGL(包括表emp、dept和sal)的相关查询：

查询一：使用子查询的方法，查找财务部cwb年龄不低于所有研发部yfb雇员年龄的雇员姓名ename、编号eid和性别sex。

查询二：使用连接查询的方式，查找财务部cwb收入income在5200元以上的雇员姓名ename及其薪水收入income支出outcome情况。

```sql
//请在下面补齐查询一的MySQL语句
/*********begin*********/
select ename,eid,sex from emp
    where did in
    (select did from dept
        where dname='cwb'
    )
/*********end*********/
    and
    birth<=all
        (select birth from emp
            where did in
                (select did from dept
                    where dname='yfb'
                )
        );
 
//请在下面输入查询二的MySQL语句
/*********begin*********/
select ename,income,outcome
    from emp,sal,dept
    where emp.eid=sal.eid and
        emp.did=dept.did and
        dname='cwb' and income>5200;
 
 
/*********end*********/
```

### 第2关：深入学习查询语句

#### 应用

在右侧代码窗口区域的指定位置编写查询语句，实现对数据库YGGL(包括表emp、dept和sal)的相关查询：
查询一：求财务部雇员的总人数；
查询二：求各部门的雇员数；
查询三：将各雇员的姓名按收入由低到高排列（提示：使用连接查询）。

```sql
//请在下面输入查询一的MySQL语句
/*********begin*********/
 
 
select count(eid)
    from emp
    where did=
        (select did
            from dept
                where dname='cwb');
 
/*********end*********/
 
//请在下面输入查询二的MySQL语句
/*********begin*********/
 
select count(eid)
    from emp
    group by did;
   
/*********end*********/
 
//请在下面输入查询三的MySQL语句
/*********begin*********/
 
select emp.ename
    from emp,sal
    where emp.eid=sal.eid
    order by income;
 
/*********end*********/
```

### 第3关：视图的创建和使用

#### 应用

请你思考，我们想限制各部门的经理只能查找本部雇员的薪水情况该怎么操作呢？比如财务部，只让财务部的经理查看本部门雇员姓名和收入、支出情况。

请你创建cx_sal视图并使用该视图查看财务部雇员薪水情况ename、income和outcome。

```sql
//请在下面输入创建cx_sal的视图的MySQL语句
/*********begin*********/
 
create or replace view cx_sal
as
    select ename,income,outcome
        from emp,sal,dept
        where emp.eid=sal.eid and
            emp.did=dept.did and
            dname='cwb';
/*********end*********/
 
//请在下面输入查询财务部雇员薪水情况视图的MySQL语句
/*********begin*********/
select * from cx_sal;
/*********end*********/
```

### 第4关：索引与完整性

#### 应用

同样的，你需要在我们已经创建好整个YGGL数据库的基础上进行以下操作：

建立索引pk_xs_bak：对emp的eid建立索引；
实现域完整性ch_tel：为emp的tel建立check约束，其值只能为0-9的数字；
实现实体完整性un_dept：为dept的dname创建唯一性索引；
实现参照完整性fk_emp：将emp中的did列为外键。

```sql
//请在下面输入创建索引的MySQL语句
/*********begin*********/
create index pk_xs_bak
on emp(eid);
 
/*********end*********/
 
//请在下面输入实现域完整性的MySQL语句
/*********begin*********/
 
alter table emp
    add(constraint ch_tel check(tel between 0 and 9));
/*********end*********/
 
//请在下面输入实现实体完整性的MySQL语句
/*********begin*********/
alter table dept
add constraint un_dept unique(dname);
 
/*********end*********/
 
//请在下面输入实现参照完整性的MySQL语句
/*********begin*********/
 
alter table emp
    add constraint sal_id foreign key(eid)
        references sal(eid);
 
/*********end*********/
```

