---
title: 一点数据库笔记
date: 2017-01-03 21:02:31
tags: 
- SQL
- Database
---

> Markdown 是个好东西，用了一段时间，这次把自己一点数据库课的笔记放上来，练习下 Markdown 。

<!--more-->

# 函数介绍

## 数据语句

* DECLARE _声明若干局部变量_
* SET _一个变量赋值_
* SELECT _多个变量赋值_
* PRINT *返回用户自定义信息*

## 循环控制语句

* BEGIN END _语句块_

* GOTO _跳转到标签_

  ```sql
  BEGIN
  GOTO skip
  select * from student
  skip:
  PRINT 'hello'
  END
  ```

* IF ELSE _条件判断_

* CASE _多分支选择_

  ```sql
  select score,
  (case
  when score > 90 then 'excellent'
  when score > 80 then 'good'
  when score > 60 then 'ok'
  else 'not well'
  end) as rate
  from student
  ```

* WHILE _循环_

  ```sql
  while
  begin
  --code here
  end
  ```

* WAITFOR _暂停_

  ```sql
  WAITFOR DELAY '11:00'
  WAITFOR TIME '01:00'
  ```

## 聚合函数

* AVG
* COUNT
* MAX
* MIN
* SUM
* DISTINCT

## 数学函数

* ABS _绝对值_
* ceiling _大于或等于_
* floor _小于或等于_
* rand _返回0-1的随机数_
* round _四舍五入到指定精度_

## 字符串函数

* ascii _第一字符的ascii值_
* char _返回ascii对应字符_
* charindex _返回匹配位置_
* itrim _去除左空格_
* rtrim _去除右空格_
* left _截断左侧指定长度字符_
* right _截断右侧指定长度字符_
* len _返回长度_
* lower _小写_
* upper _大写_
* reverse _转置_
* replace _指定字符替换_
* space _指定空格数_
* stuff _替换字符串的指定范围_
* substring _返回指定范围字符串_

## 日期和时间函数

* dateadd _给指定日期添加一段时间_
* datediff _两日期相减_
* datename _返回指定日期的部分_
* day _指定日期的天数_
* dayofyear _年内天数_
* month _返回日期的月份_
* year _返回日期的年份_
* getdate _返回系统时间_
* datepart _返回指定部分整数_
* isDate _检测日期有效性_

# 语句提升

## 约束与规则

### 规则

1. 规则只允许在当前数据库创建
2. 规则不能绑定到数据类型 char、 int、 image、 text 中

* 创建规则

```sql
create rule 规则名
as 规则
/* 规则可以是where语句任何有效的表达式 */
```

* 绑定规则

```sql
use 数据库名
exec sp_bindrule '规则名','数据库表字段'
```

* 解绑规则

```sql
use 数据库名
exec sp_unbindrule '数据库表字段'
```

* 删除规则

```sql
drop rule '规则名'
```

### 约束

* 添加check约束

```sql
alter table 表名
add [constraint 约束名] check(约束)
```

* 删除check约束

```sql
alter table 表名
drop constraint 约束名
```

### 区别

1. 约束和表的定义联系，删除表的同时约束也删除。规则是单独存储的数据库对象，独立于表外，删除表时并不能删除规则。
2. 一个字段可有多个约束，但只能有一个规则。

## SELECT高级查询

### IN, NULL

* IN _查询符合列表中任何一个值的记录_

```sql
select * from table1 where score in (70,80,90);
select * from table2 where score in (select score from table1);
```

* NULL | NOT NULL _字段是否为空_

```sql
select * from table1 where items in null;
```

### SELECT

> 用于将查询结果存储到另一个表

```sql
select top 5 *
into table3
from table2;
```

### GROUP BY

> 用于数据汇总

```mssql
select name,avg(age) as avg_age
from student
group by name;
```

## 嵌套查询

* 子查询作为新增列 _作为外层select语句的列值_

```sql
select avg_score = (
select avg(score)
  from score
)from score
order by score.id;
```

* 使用IN关键字 _主要用于where子句后面的子查询。_

```sql
select a.name
from student as a
where a.id in(
select b.id
  from score as b
where b.score = 80
)order by a.id;
```

* 比较运算符

```sql
select a.name
from student as a
where a.id in(
select b.id
from score as b
where b.score <= 80
) order by a.id;
```

* BETWEEN

```sql
select a.name
from student as a
where a.id in(
select b.id 
from score as b
where b.score
between 80 and 90
) order by a.id;
```

* EXISTS

```sql
select *
from table1
where exists(
select score
from table1
where score = 80)
/* IN和EXISTS都代表的是子查询存在某个值，但是IN用的时候，子查询只能是一个字段，但是EXISTS可以用多个字段。 */
```

## 多表连接

### JOIN...ON

#### 举例

* 表A

| snum | name | age  | sex  |
| :--: | :--: | :--: | :--: |
|  1   |  AA  |  12  |  M   |
|  2   |  BB  |  13  |  F   |
|  3   |  CC  |  24  |  M   |
|  4   |  DD  |  21  |  F   |

* 表B

| snum | score |
| :--: | :---: |
|  1   |  10   |
|  2   |  20   |
|  5   |  40   |

#### inner join

| snum | name | age  | sex  | snum | score |
| :--: | :--: | :--: | :--: | :--: | :---: |
|  1   |  AA  |  12  |  M   |  1   |  10   |
|  2   |  BB  |  13  |  F   |  2   |  20   |

#### left join

| snum | name | age  | sex  | snum | score |
| :--: | :--: | :--: | :--: | :--: | :---: |
|  1   |  AA  |  12  |  M   |  1   |  10   |
|  2   |  BB  |  13  |  F   |  2   |  20   |
|  3   |  CC  |  24  |  M   | null | null  |
|  4   |  DD  |  21  |  F   | null | null  |

#### right join

| snum | score | snum | name | age  | sex  |
| :--: | :---: | :--: | :--: | :--: | :--: |
|  1   |  10   |  1   |  AA  |  12  |  M   |
|  2   |  20   |  2   |  BB  |  13  |  F   |
|  5   |  40   | null | null | null | null |

#### full join

| snum | name | age  | sex  | snum | score |
| :--: | :--: | :--: | :--: | :--: | :---: |
|  1   |  AA  |  12  |  M   |  1   |  10   |
|  2   |  BB  |  13  |  F   |  2   |  20   |
|  3   |  CC  |  24  |  M   | null | null  |
|  4   |  DD  |  21  |  F   | null | null  |
| null | null | null | null |  5   |  40   |

### UNION

> 拼接字段相同的表

```sql
select * from student as a
union
select * from student as b
```

### 数据操纵进阶

## insert

### 插入多行

```sql
insert into table2
select name,sex,phone,email
from table2
```

### select ... into

```sql
select * into table1 from table2
```

## update

### 基于级联

```sql
update table1 set table1.c = table2.c
from table1 inner join table2
on table1.a = table2.a
where table1.c is null
```

### 带有output

```sql
-- 查询新行的属性
insert into table1(a) output inserted.a values('123')
-- 查询旧行的属性
delete into table1 output deleted.a where a = '123'
-- 返回修改后的值
update table1 set a = 'b' output inserted.a where a = '123'
-- 返回修改前的值
update table1 set a = 'b' output deleted.a wherer a = '123'
```

# 视图、索引、触发器

## 视图

* 创建视图

```sql
create view 视图名
as
select语句
```

* 视图结果集排序

```sql
create view stu1
as
select top 3 * from table2
order by id
```

* 多张表进行视图查询

```sql
create view stu2
as
select b.score,b.name,a.name,sex,age
from student as a
inner join
score as b
on a.id = b.id
```

* 修改视图

```sql
alter view 视图名
as
select语句
```

* 删除视图

```sql
drop view 视图名
```

### 增删改

1. 视图可以对基本表的数据进行查询，还可以向基本表增删改
2. select 子句不可用聚合函数
3. 不能包含算式表达式结果的列
4. 视图引用多表，无法使用delete

## 索引

### 优点

1. 通过创建唯一索引，可以保证数据记录的唯一性。
2. 可以大大加快数据检索速度。
3. 可以加速表与表之间的连接，这一点在实现数据的参照完整性方面有特别的意义。
4. 在使用order by和group by子句中进行检索数据时，可以显著减少查询中分组和排序的时间。
5. 使用索引可以在检索数据的过程中使用优化隐藏，提高系统性能。

### 缺点

1. 创建索引要花费时间和占用存储空间。
2. 建立索引加快了数据检索速度，但是减慢了数据修改速度。

### 不应创建索引列的情况

1. 很少或从来不在查询中引用的列，因为系统很少或从来不根据这个列的值去查找数据行。
2. 只有两个或很少几个值的列，例如性别。
3. 以bit、text、image数据类型定义的列。
4. 数据行输很少的表一般也没有必要创建索引。

## 触发器

### DML触发器

* after触发器 _记录改变后才激活触发器_

> after触发器包括 insert、delete、update触发器

```sql
after insert
as
begin
SET NOCOUNT ON;
/* process */
end
go
```

* instead of 触发器 _直接执行触发器，不执行sql语句_

> instead of 触发器包括insert、delete、update触发器

```sql
instead of insert
as
begin
SET NOCOUNT ON;
/* process */
if @age > 25
print 'Too old!'
else
insert into score(name,age) values(@name,@age)
end
go
```
