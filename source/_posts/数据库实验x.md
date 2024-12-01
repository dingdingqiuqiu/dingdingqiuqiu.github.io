---
title: 数据库实验x
mathjax: true
date: 2024-10-28 21:10:31
tags:
categories: 数据库
---

本文基于头歌平台的数据库实验X记录SQL语言的学习过程。

<!--more-->

## 数据库实验一





## 数据库实验二 数据表中数据的插入、修改和删除

### 第1关：数据表中插入一条记录,对指定字段赋值

#### 插入语句

```sql
 INSERT  INTO  <表名>  (<字段1>[,<字段2>…]) VALUES (<表达式1>[,<表达式2>…])
```

#### 应用

在library数据库的reader数据表中插入一条数据
姓名xm为林团团，电话号码dhhm为13507311234，其余字段取默认值
显示数据表的所有数据

```sql
use library;
 #代码开始
insert into reader (xm,dhhm) values ('林团团','13507311234');

# SELECT * FROM reader;
```

> 注意这里字段名和值都要用括号包起来。

### 第2关：数据表中插入一条记录，对所有字段赋值

#### 对所有字段赋值的插入语句

```sql
INSERT  INTO  <表名> VALUES (<表达式1>[,<表达式2>…])
```

#### 应用

在reader数据表中插入一位读者
读者证号是2，姓名是陈洁，性别是女，身份是教研人员，电话号码是13319551234

提示：reader数据表各个字段顺序为dzzh,xm,xb,sf,dhhm

```sql
 use library;
 #代码开始
insert into reader (xm,xb,sf,dhhm) values('陈洁','女','教研人员','13319551234');
 #代码结束
 select * from reader;
```

### 第3关：数据表中插入多条记录

#### 插入多条数据

```sql
INSERT  INTO  <表名>  (<字段1>[,<字段2>…])
VALUES (<表达式11>[,<表达式12>…])，
   (<表达式21>[,<表达式22>…])，
   (<表达式31>[,<表达式32>…])……
```

#### 应用

本关任务：在reader数据表中插入多条数据
姓名是黄小小，性别是男，身份是研究生，电话是13316789987
姓名是刘大任，性别是男，身份是工作人员，电话18012341234
姓名是邓朝阳，性别是女，身份是研究生，电话是17716554432
提示：数据表reader的姓名、性别、身份、电话号码字段是xm,xb,sf,dhhm
读者证号是自增字段，其值会自动产生

```sql
use library;
#代码开始
insert into reader (xm,xb,sf,dhhm) values('黄小小','男','研究生','13316789987'),('刘大任','男','工作人员','18012341234'),('邓朝阳','女','研究生','17716554432');
 #代码结束
 select * from reader;
```

### 第4关：在数据表中修改单条数据记录的单个字段的值

#### 更新字段

```sql
UPDATE <表名>  SET<字段1>=<表达式1>  [,<字段2>=<表达式1>……]  [WHERE <条件>]
```


对于指定数据表中符合条件的记录，用指定的表达式的值来更新指定的字段。使用UPDATE命令可以一次更新多个字段的值。WHERE <条件>用来指定更新的条件。

#### 应用

将reader数据表中林团团xm的电话号码dhhm修改为17718991989

```sql
 use library;
#代码开始
update reader set dhhm=17718991989 where xm='林团团';
 
 
 #代码结束
 select * from reader;
```

### 第五关：修改多字段信息

#### 应用

修改读者表reader的陈洁xm的电话号码dhhm为13315667745，身份为工作人员。

```sql
 use library;
 #代码开始
 update reader set dhhm =13315667745,sf='工作人员'where xm='陈洁';
 #代码结束
 select * from reader;
```

### 第6关：修改数据表的多条记录

#### 应用

将每位读者reader的读者证dzzh号加十

```sql
use library;
#代码开始
update reader set dzzh=dzzh+10;
#代码结束
select * from reader;
```

> 注意SQL语言中不能使用`dzzh+=10`

### 第7关：删除数据表的记录

#### 删除语句

```sql
delete from 表名 where 条件表达式;
```

#### 应用

删除读者reader数据表中的陈洁xm

```sql
 use library;
 #代码开始
 delete from reader where xm='陈洁';
 #代码结束
select * from reader;
```

> 注意是直接`delete from 表名`

### 第8关：删除数据表的多条记录

#### 应用

删除读者reader中的所有研究生sf

```sql
 use library;
 #代码开始
 delete from reader where sf='研究生';
 #代码结束
 select * from reader;
```

### 第9关：删除数据表的所有数据

#### 应用

删除所有读者reader

```sql
 use library;
 #代码开始
 delete from reader;
 #代码结束
 select * from reader;
```

非常好第九关，使我删库跑路。

## 数据库实验五 函数

### 第1关：数值函数

#### 函数用法

四舍五入的函数

```sql
ROUND(X,D)
```

返回X，其值保留到小数点后D位，而第D位的保留方式为四舍五入。
若D的值为0,则对小数部分四舍五入。
若将D设为负值，保留X值小数点左边的D位

```sql
TRUNCATE(X,D)
```

返回被舍去至小数点后D位的数字X。
若D的值为0,则不带有小数部分。
将D设为负数,则截X小数点左起第D位开始后面所有低位的值

总结：

`ROUND`函数四舍五入，`TRUNCATE`函数直接舍去。

#### 应用

![image-20241029185641134](https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VYOHhiUnVJU2VGTXVPbWtEUWJ2LWs0QnBIZ3l4b2l4S28yRXVyMVhVYjc4clE_ZT12ZGJhZko.png)

```SQL
SELECT gyxm,round(gz*0.005,0) as kf FROM gzry;

SELECT gyxm,truncate(gz*0.005,0) as kf FROM gzry;
```

> 不知道为啥，0.05才给过。

### 第2关：字符串函数一

#### `concat`字符串拼接函数

```sql
concat(<字符串1>,<字符串2>,<字符串3>)
```

将各个字符串连接起来

#### `rpad`填充字符串到指定长度后返回

```sql
rpad(<字符串>,<长度>,<填充字符>)
```

返回字符串，右面用填充字符填补，直到指定长度的字符串

#### `left`返回字符串左边的指定长度的字符

```sql
LEFT(<字符串>,<长度>)
```

返回字符串的最左边的指定长度的字符

#### `char_length`返回字符串的长度

```sql
char_length(<字符串>)
```

返回字符串的长度，即字符个数。

#### 应用

![image-20241029192950163](https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VlWGdxVFJSYzlsRGg2djZXdVltWllnQldoeHIwSHhaSFMzUm90THAzMFJzU1E_ZT16bWVoQlU.png)

```sql
use sale
 #代码开始
 #答案一
 SELECT concat(rpad(bm,4,'　'),rpad(gyxm,4,'　'),dh) as 'ygxx' FROM gzry ORDER BY bm;

 #答案二
 SELECT gyxm,dh FROM gzry WHERE left(gyxm,1)='王' and char_length(gyxm)=3;
 
 #代码结束

```

### 第3关：字符串函数二

#### `insert`字符串插入函数

```sql
insert(<字符串>,<位置>,<长度>,<插入的字符串>) 
```

返回一个字符串，将字符串中指定位置的指定长度的字符删除，插入指定的字符串。

#### `space`空格生成函数

```sql
space(<整数>)
```

返回指定整数的空格

#### `mid`返回中间指定长度的字符串

```sql
mid(<字符串>,<指定位置>,<指定长度>)
```

返回字符串从指定位置开始的指定长度的字符串

#### `right`返回右边指定长度的字符串

```sql
right(<字符串>,<指定长度>)
```

返回字符串右边的指定长度的字符串

#### `replace`字符串替换函数

```sql
replace(<字符串>,<源字符串>,<目标字符串>)
```

返回一个字符串，将字符串中所有的源字符串用目标字符串代替。

#### 应用

![image-20241029201938073](https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0Vhbnpqb0FOM1BKTW9qYWswTmE2UDFvQjZuMDdoclpXLTJIYXVjUVFfTlR6ZUE_ZT1zbWlpbDY.png)

```sql
# 第一问：显示每位顾客的姓名，两个字的中间插入两个空格，三个字的直接显示,列名为xm
select case
	when char_length(name)= 2 then
		insert(name,2,0,'  ')
	else name
end as xm from gk;
```

> ​	这里关键点有三个：

> 1. `case- when - then - else - end`语句：注意不要漏掉任何一块，漏掉即无法运行

> ​	2.注意`insert`函数可以在以上控制流中并非独立单元，而是返回一个处理好的字符串

> ​	3. 注意数据插入时，从角标2开始插入

```sql
# 第二问显示每位顾客的姓名和电话（dh)，电话按照999-9999-9999的格式显示
# 解法1
# 使用插入实现
select name,insert(insert(tel,4,0,'-'),9,0,'-')as dh from gk;

```

> 还有就是不要忘了用as命名insert返回的字符串的字段名，不是原来的了

```sql
# 第二问显示每位顾客的姓名和电话（dh)，电话按照999-9999-9999的格式显示
# 解法2
# 使用拼接实现
select name,concat(left(tel,3),'-',mid(tel,4,4),'-',right(tel,4)) as dh from gk;

```

```sql
# 第三问将顾客数据表中单位(dept)中的新一佳用佳惠替换
select name,(replace(dept,'新一佳','佳惠') as dept) from gk;
```

> 这个很简单，直接替换源字符串为目的字符串即可

### 第4关：日期函数

#### `year`函数返回年号

```sql
year(日期)
```

返回日期的年号

#### `month`函数返回月号

```sql
month(日期)
```

返回日期的月号

#### `datediff`函数返回日期相差天数

```sql
datediff(日期1，日期2)
```

返回两个日期相差的天数

#### 应用

![image-20241029212324174](https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VSLXphMERSa0U5SmpTcnAxaXR6RDhvQnlvSm04OHBZS1FMOHRfSTNHM2JqaXc_ZT1sajdPZUo.png)

```sql
select month(xsrq) as yf,sum(sjfk) from xsd group by yf;
```

> 根本不会`group by`相关操作，要学下了

![image-20241029214609547](https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VlSEZoWEFTa0poTXU5Z0s4NmtzODVvQnFVaWtfMTYzb0dBV1NJZkc3eDl0dVE_ZT04NGh3TG4.png)

```sql
select borrow.dzzh,
if(datediff(borrow.hsrq,borrow.jsrq)>30,(datediff(borrow.hsrq,borrow.jsrq)-30)*book.sj*0.01,0) 
as fk from borrow join book on borrow.txm=book.txm
where datediff(borrow.hsrq,borrow.jsrq)>30;
```

> if语句块结构:
>
> if(条件，条件为真时行为，条件为假时行为)
>
> 查询两个表**必须**首先进行连接：
>
> 表1 join 表2 on 表一表二的相同字段相等(连接条件)

### 第5关：条件函数

#### `if`条件函数

```sql
if(关系表达式,值1,值2)
```

当条件表达式为真，返回值1，否则返回值2

#### `when`条件函数

```sql
case <表达式> when <条件1> then <值1>
when <条件2> then <值2>
……
else <值n+1>
end
```

若表达式的值为条件1则返回值1，否则若表达式的值为条件2则返回值1……若都不相同则返回值n+1

#### 应用

![image-20241031140921423](https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0Vmc0R3VTc2UWN0Smdtb19wb2czaXQwQnV6Mk03SkRPY0I5WVhqekpOTDRTeHc_ZT1RZEs3UjI.png)

```sql
select gyxm,if(gz<2000,50,200) as fy from gzry;
```

![image-20241031140940789](https://dlink.host/1drv/aHR0cHM6Ly8xZHJ2Lm1zL2kvYy9mYmQ0NGEwNjM2YTQyNDJlL0VWc2tMdnNpeDNsSG90eF84NEp4QUhzQjl1OGVfa2YzbjA3Q1BxNnZwREV2MGc_ZT1vaTV5SWc.png)

```sql
select gyxm,case
when bm='销售部' then 1000
when bm='办公室' then 800
when bm='采购部' then 500
when bm='仓库'   then 300
end as jt from gzry;
```

## 数据库实验六 索引

#### 建立数据表的同时建立索引

```sql
CREATE  TABLE  table_name 
    ([col_name data_type]
    [PRIMARY|UNIQUE][|INDEX|KEY]     
    [index_name] (index_col_name [length])
    [ASC | DESC]) 
```

table_name数据表的名称
primary主索引
unique唯一索引
index_name索引名
index_col_name索引列的名称

显示索引

```sql
 SHOW 
{INDEX|INDEXES|KEYS}
{FROM|IN}   [db_name .]table_name 
[[FROM|IN] db_name ]
[\G ]
```

#### 应用

在sale数据库中，建立供应商数据表gys，包括供应商号gysh 字符型4位、公司名称 可变长字符型20位、电话 可变长字符型11位、地址 可变长字符型20位、联系人 可变长字符型4位、手机 字符型11位 字段，同时根据供应商号字段建立主索引。

```sql
create table gys(
	gysh char(4) primary key,
    gmsc varchar(20),
    dh varchar(11),
    dz varchar(20),
    lxr varchar(4),
    sj char(11)
);
show index from gys;
```

> 声明的多个类型间使用逗号分割
>
> 主索引的声明是`primary key`
>
> 索引的查看是`show index from 表名`,不要漏掉`from`
>
> `create`语句的`;`别忘了。

### 第2关：在已有的数据表建立索引

#### 主索引建立

```sql
alter table 数据表名 add primary key 索引名(字段名)
```

#### 其他索引建立

##### 普通索引

```sql
create index 索引名 on 表名(字段名);
```

##### 唯一索引

```sql
create unique index 索引名 on 表名(字段名);
```

#### 应用

第一题
在xsdmx数据表根据销售单编号xsdh和序号xh两个字段建立主索引xsdxh

```sql
alter table xsdmx add primary key xsdxh(xsdh,xh);
```

> 主索引建立方式区别其他索引

第二题
在xsdmx数据表根据商品编号sph字段建立普通索引sphsy。

```sql
create index sphsy on xsdmx(sph);
```

第三题
在商品sp数据表根据商品名spm字段建立唯一索引spmsy。

```sql
create unique index on sp(spm);
```

### 第3关：删除索引

#### 删除索引

```sql
drop index 索引名 on 表名;
```

#### 应用

删除sp商品数据表的索引spmsy

```sql
drop index spmsy on sp;
```

## 实验七 数据完整性

### 第1关：通过主索引设置实体完整性

#### 主索引建立

```sql
alter table 数据表名 add primary key 索引名(字段名)
```

#### 应用

对于图书数据表book（已经建立并插入记录），根据条形码（txm)、建立一个主索引,保证数据表中每本书的条形码是唯一的，即实体完整性

```sql
alter table book primary key pk(txm);
```

### 第2关：通过check设置域完整性

#### 设置`check`约束

```sql
alter table <数据表名> add constraint <约束名> check <约束条件>
```

#### 应用

对于图书数据表book（已经建立并插入记录），对于价格字段sj设置约束sjgd,要求价格必须大于0且小于等于5000

```sql
alter table book add constraint sjgd check (0<sj and sj<=5000);
```

> 注意这里的约束条件需要用小括号包裹

### 第3关：设置借阅表和读者表的参照完整性

#### 设置参照完整性

在数据表之间增加参照完整性的命令如下所示

```sql
alter table 子表 add
[CONSTRAINT 外键名]
FOREIGN KEY (字段名) 
REFERENCES 主表(主键列)
on delete restrict|cascade|set null
on update delete restrict|cascade|set null
```

> `constraint`约束

在设置参照完整性后，当主表中没有相关数据时，子表中无法插入对应的记录。

如果delete设置为cascade,在删除主表数据的时候，子表的数据将同时被删除;
如果delete设置为restrit,子表中存在数据时，主表的数据将无法删除
如果delete设置为set null,在删除主表数据的时候，子表的数据被设置为null值
(该列可以设置为null值的情况下)

如果update设置为cascade,在修改主表的关键字段的值的时候，子表中相关数据的字段的值将同时被修改;
如果update被设置为restrit,子表中存在数据时，主表的相关数据的关键字段的值将无法修改
如果update设置为set null,在修改主表的关键字段的值的时候，子表中相关数据的字段的值将被设置为null
(该列可以设置为null值的情况下)

#### 应用

在借阅表和读者表设置参照完整性
当删除读者表的数据时，借阅表的相关记录一起删除
当修改读者表的读者证号时，借阅表的相关记录的读者证号一起被修改

```sql
  use library;
 #代码开始
 alter table borrow add 
 foreign key (dzzh) references reader(dzzh)
 on delete cascade on update cascade;
 #代码结束
delete from reader where dzzh="001";
update reader set dzzh="111" where dzzh="002";
select * from borrow;
```

> 注意所有字段名都要用括号包裹起来

### 第4关：设置借阅表和图书表的参照完整性*

#### 应用

在借阅表和图书表设置参照完整性
当借阅表有某个条形码的记录，就不能删除图书表中相关的图书，也不能修改图书表中相关图书的条形码。

```sql
 use library
 #代码开始
 ALTER TABLE borrow ADD
 FOREIGN KEY (txm) REFERENCES book(txm) 
 ON DELETE RESTRICT ON UPDATE RESTRICT;
 #代码结束
 delete from book where txm="P0000001";
```

### 第5关：建立数据表并设置参照完整性*

#### 应用

建立期刊qk数据表和期刊借阅qkjy数据表
期刊qk数据表有6个字段，如下所示
期刊条码qktxm varchar 10
期刊名称qkmc varchar 20
刊号kh varchar 10
卷号jh varchar 10
出版单位cbdw varchar 20
价格jg decimal4,1字段
期刊借阅qkjy数据表有4个字段，如下所示
读者证号dzzh tinyint 3 unsigned zerofill
期刊条码qktm varchar 10
借阅日期jyrq date
还书日期 hsrq date
在建立期刊借阅数据表时，与读者表建立关联。
当修改读者表的读者证号，借阅期刊表的相关会删除。当删除读者表的读者证号，借阅期刊表的相关记录会删除。
在建立期刊借阅数据表时，同时与期刊表建立关联。不允许修改和删除期刊数据表的相关数据。
注意：期刊数据表需要根据期刊号建立主索引

```sql
use library;
#代码开始
CREATE TABLE qk (
  qktxm VARCHAR(10) PRIMARY KEY,
  qkmc VARCHAR(20) ,
  kh VARCHAR(10) ,
  jh VARCHAR(10) ,
  cbdw VARCHAR(20) ,
  jg DECIMAL(4,1) );
CREATE TABLE qkjy (
  dzzh TINYINT(3) UNSIGNED ZEROFILL,
  qktxm VARCHAR(10),
  jyrq DATE,
  hsrq DATE,
  FOREIGN KEY (dzzh) REFERENCES reader(dzzh) ON DELETE CASCADE ON UPDATE CASCADE,
  FOREIGN KEY (qktxm) REFERENCES qk(qktxm) ON DELETE RESTRICT ON UPDATE RESTRICT
) ;
#代码结束
show create table qkjy;
```

### 第6关：删除参照完整性

#### 删除外键

```sql
alter table <数据表名> drop foreign key <外键名>
```

#### 应用

删除借阅数据表和图书数据表的外键，名字为`borrow_ibfk_1`

```sql
use library; 
#代码开始
ALTER TABLE borrow DROP FOREIGN KEY borrow_ibfk_1;
#代码结束
show create table borrow;
```

## 实验八 视图

### 第1关：建立基于单表的视图，在视图中插入、删除和修改记录

#### 应用

第一题
建立视图ckyg，查询gzry数据表中部门bm为仓库的员工的所有字段的信息

```sql
crete view ckyg as select * from gzry where bm='仓库';
```

第二题
在视图ckyg中，插入gyh雇员号为019，姓名gyxm为李盛，部门bm为仓库的数据。

```sql
insert into ckyg(gyh,gyxm,bm)values('019','李盛','仓库');
```

第三题
在视图ckyg中，删除姓名为赵国庆的数据

```sql
delete from ckyg where gyxm='赵国庆';
```

第四题
在视图ckyg中，将王文武的电话改为13319660678

```sql
update ckyg set dh='13319660678' where gyxm='王文武';
```

### 第2关：根据多个数据表建立视图

#### 应用

建立xsdxx视图，包含销售单号xsdh、雇员号gyh、雇员姓名gyxm、会员号hyh、会员姓名name、销售日期xsrq、实际付款sjfk字段。

```sql
 use sale;
 #代码开始
 # describe gzry;
 # describe xsd;
 # describe gk;
create view xsdxx as select xsd.xsdh,xsd.gyh,gzry.gyxm,xsd.hyh,gk.name,xsd.xsrq,xsd.sjfk
from xsd
left join gzry on  gzry.gyh=xsd.gyh
left join gk on  gk.hyh=xsd.hyh;
 
 #代码结束
 select * from xsdxx;
```

### 第3关：根据视图建立视图*

#### 应用

第一题：
根据xsdxx视图建立视图xsdhytj,显示会员号hyh，姓名name和实际付款sjfk的合计金额(命名为hjje)
按合计金额的降序排列

```sql
CREATE VIEW hytjcx AS
SELECT hyh, name, SUM(sjfk) AS hjje
FROM xsdxx
GROUP BY hyh, name
ORDER BY hjje DESC;
```

> 注意这里用到了sum函数，因此需要使用`group by`分组

第二题：
根据xsdxx视图建立视图xsdgytj,显示雇员号gyh，姓名xm和实际付款sjfk的合计金额(命名为hjje)
按合计金额的降序排列

```sql
CREATE VIEW gytjcx AS
SELECT gyh, gyxm, SUM(sjfk) AS hjje
FROM xsdxx
GROUP BY gyh
ORDER BY hjje DESC;
```

### 第4关：更新视图

#### 应用

在视图xsdxx中，将工作人员gyxm王强的销售日期xsrq2015-6-3的会员名name刘海东的订单的实际付款sjfk设置为800
观察视图xsdgytj和xsdhytj的变化

```sql
use sale;
#代码开始
 
UPDATE xsdxx
SET sjfk = 800
WHERE gyxm = '王强' AND xsrq = '2015-6-3' AND name = '刘海东';
 
 
 #代码结束
select * from xsdhytj;
select * from xsdgytj;
```

## 实验十 用户管理和授权

### 第1关：建立用户并授权

#### 应用

本关任务：建立用户admin,在所有机器上均可登录
对所有数据库的数据表都有权限

```sql
 
 #代码开始
 #建立用户
create user admin@'%' identified by '123456';
 #用户授权
grant all on *.* to admin;
 
 #测试
 select host,user,Update_priv,Alter_priv from mysql.user where user='admin' ;
 
```

### 第2关：建立用户,授权其对数据表的查询

建立用户user1,密码为888888，在本机(127.0.0.1)登录，对province数据库的jdxx数据表有查询权限

```sql
use province
 #代码开始
 #建立用户
CREATE USER 'user1'@'127.0.0.1' IDENTIFIED BY '888888';
 
 
 #用户授权
 GRANT SELECT ON province.jdxx TO 'user1'@'127.0.0.1';
 
 
 #代码结束
select host,db,table_name,Table_priv   from mysql.tables_priv  where  user='user1';
```

### 第3关：建立用户，有部分权限

本关任务：建立用户user2，在本机（127.0.0.1)登录，密码为666666.对数据库province库的所有数据表有所有的权限，对数据库library库的book表有查询的权限

```sql
 
#代码开始
#建立用户
CREATE USER 'user2'@'127.0.0.1' IDENTIFIED BY '666666';
 
#用户授权
GRANT ALL PRIVILEGES ON province.* TO 'user2'@'127.0.0.1';
GRANT SELECT ON library.book TO 'user2'@'127.0.0.1';
 
 
 
 #代码结束
select host,db,user,Delete_priv,Index_priv from mysql.db where user='user2' ;
select host,db,table_name,Table_priv   from mysql.tables_priv  where  user='user2';
```

