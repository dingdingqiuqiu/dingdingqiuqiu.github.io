---
title: MySQl-数据库和表的基本操作
mathjax: true
date: 2024-10-28 20:20:17
tags:
categories: 数据库
---

本文记录数据库和表的基本操作，包括数据的插入，更新，删除等操作。

<!--more-->

## MySQl-数据库和表的基本操作（一）

### 第三关，添加和删除字段

任务描述
本关任务：分别在表的最后一列、第一列和指定列后添加新的字段，并删除表中的指定字段。

相关知识
为了完成本关任务，你需要掌握：
1.如何在指定位置添加新的字段，
2.如何删除指定的字段。

添加字段
因为甲方的业务需求是不停变化的，所以在数据库操作中，添加字段可是常有的事。一个完整的字段包括：字段名、数据类型和完整性约束。

语法规则为： ALTER TABLE 表名 ADD 新字段名 数据类型 [约束条件] [FIRST|AFTER] 已存在字段名; 。
以下是在 MySQL 中常用的约束。

NOT NULL 约束：确保某列不能有 NULL 值。

DEFAULT 约束：当某列没有指定值时，为该列提供默认值。

UNIQUE 约束：确保某列中的所有值是不同的。

PRIMARY Key 约束：唯一标识数据库表中的各行/记录。

CHECK 约束：CHECK 约束确保某列中的所有值满足一定条件。

在表的最后一列添加字段
只要不做[FIRST|AFTER]的位置说明，在添加字段时MySQL会默认把新字段加入到表的最后一列。

举个例子：
现在我们要把字段prod_country添加到表Mall_products的最后一列。表结构如下：



输入命令：
ALTER TABLE Mall_products ADD prod_country varchar(30);
执行结果如下所示：



在表的第一列添加字段
如果我们想在第一列添加新的字段，只需做FIRST的位置说明。

举个例子：
现在我们要把字段prod_country添加到表Mall_products的第一列。

输入命令：
ALTER TABLE Mall_products ADD prod_country varchar(30) FIRST;
执行结果如下所示：



在表的指定列后添加字段
如果我们想在某一列后面添加新的字段，只需做AFTER的位置说明，然后注明你想让它添加在哪个字段的后面即可。

举个例子：
现在我们要把字段prod_country添加到表Mall_products的 prod_name字段的后面。

输入命令：
ALTER TABLE Mall_products ADD prod_country varchar(30) AFTER prod_name;
执行结果如下所示：



总之，想要添加新的字段，记住绿色框里的语法规则就能记住三种位置的添加方式。
删除字段
有添加的需求就会有删除的需求。删除一个字段就是将数据表中的某个字段从表中移除。

语法规则为： ALTER TABLE 表名 DROP 字段名; 。
举个例子：
现在我们要把字段prod_price从表Mall_products中删除。表结构如上图结果所示。

输入命令：
ALTER TABLE Mall_products DROP prod_price;
执行结果如下所示：



字段prod_price成功删除！

接下来你们可以自行体验一下了！

编程要求
根据提示，在右侧编辑器补充代码:

在数据表tb_emp的Name字段后添加字段Country，数据格式为varchar(20)；

删除数据表tb_emp中的字段Salary。

数据表结构如下:



测试说明
我会对你编写的代码进行测试，最终结果会如下图所示：

如果我们想在第一列添加新的字段，只需做FIRST的位置说明。

举个例子：
现在我们要把字段prod_country添加到表Mall_products的第一列。

输入命令：
ALTER TABLE Mall_products ADD prod_country varchar(30) FIRST;

开始你的任务吧，祝你成功！

```sql
USE Company;

#请在此处添加实现代码
########## Begin ##########
# 现在我们要把字段prod_country添加到表Mall_products的最后一列。
ALTER TABLE Mall_products ADD prod_country varchar(30);

########## add the column ##########

 
########## delete the column ##########



########## End ##########

DESCRIBE tb_emp;
```

### 1. 在 `tb_emp` 表的 `Name` 字段后添加 `Country` 字段

要在 `Name` 字段后添加 `Country` 字段，并将其数据类型设置为 `varchar(20)`，可以使用 `AFTER` 关键字指定位置：

```sql
ALTER TABLE tb_emp ADD Country VARCHAR(20) AFTER Name;
```

### 2. 删除 `tb_emp` 表中的 `Salary` 字段

要删除 `tb_emp` 表中的 `Salary` 字段，可以使用以下命令：

```sql
ALTER TABLE tb_emp DROP COLUMN Salary;
```

这

### 第四关

根据提示，在右侧编辑器补充代码:

将数据表tb_emp的Name字段移至第一列，数据格式不变；

将DeptId字段移至Salary字段的后边，数据格式不变。

数据表结构如下:

```sql
ALTER TABLE tb_emp MODIFY COLUMN Name VARCHAR(50) FIRST;
ALTER TABLE tb_emp MODIFY COLUMN DeptId INT AFTER Salary;

```

###  第五关：删除表的外键约束

编程要求
我们已经为你建立了主表tb_dept和子表tb_emp，在表tb_emp上添加了名称为emp_dept的外键约束，外键名称为DeptId，依赖于表tb_dept的主键Id，下面那是两张表的结构展示：

请你根据提示，在右侧编辑器Begin-End中补充代码:

删除数据表tb_emp的外键约束emp_dept。
测试说明
我会对你编写的代码进行测试，最终结果会如下图所示：

```sql
USE Company;

#请在此处添加实现代码
########## Begin ##########

########## delete the foreign key ##########


ALTER TABLE tb_emp DROP FOREIGN KEY emp_dept;



########## End ##########
SHOW CREATE TABLE tb_emp \G;

```

## MySQl-数据库和表的基本操作（二）

### 第1关：插入数据

#### 插入语句

```sql
INSERT INTO 表名 (字段名) VALUES (内容);
```

#### 应用

![image-20241029153918727](https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VjVXp4emJoa25WRXNWV0tENl9YWnZBQkd6dzhvRUhFTEVqWXNBNG96R0xjM3c_ZT1INHBmYUg.jpg)

```sql
USE Company;

#请在此处添加实现代码
########## Begin ##########

########## bundle insert the value 
INSERT tb_emp (Id,Name,DeptId,Salary) VALUES (1,'Nancy',301,2300.00),(2,'Tod',303,5600.00),(3,'Carly',301,3200.00);

########## End ##########
SELECT * FROM tb_emp;
```

### 第2关：更新数据

#### 更新语句

```sql
UPDATE 表名 SET 字段名=内容 WHERE 限制条件;
```

#### 应用

![image-20241029155914738](https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VXNE5RZzd1RVJCQ25rN052bld2R2NjQjF4N2JnX0NRVXdXQmpSQ2RySHdQWFE_ZT1jazh2N0s.jpg)

```sql
USE Company;

#请在此处添加实现代码
########## Begin ##########

########## update the value ##########
UPDATE tb_emp SET Name='Tracy',DeptId=302,Salary=4300.00 WHERE Id=3;

########## End ##########

SELECT * FROM tb_emp;
```

### 第3关：删除数据

#### 删除语句

```sql
DELETE FROM 表名 WHERE 限制条件;
```

>  TRUNCATE TABLE 语句也可以用来删除表中的所有记录。但是与 DELETE 不同的是，TRUNCATE TABLE 语句直接删除的是表，而不是表中的内容，删除结束后还会重新创建一个表。所以它的执行速度会比 DELETE 语句快。 语法为：`TRUNCATE TABLE 表名;`

#### 应用

![image-20241029160607922](https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VSYmY1MUxqaVQ5T3BzY1hyYlo3RFJvQnl3SUpaX0NGNTBxRFpiSjQwNDFubkE_ZT1keE96MFg.jpg)

```sql
USE Company;

DELETE FROM tb_emp WHERE Salary>3000;

SELECT * FROM tb_emp;
```

