---
title: SQL干货篇一
date: 2017-04-06 22:33:37
categories: 数据库干货篇
tags: SQL
---
# SQL系列之基本操作

## 新建表
>**CREATE TABLE <NAME> 
>[<列名><数据类型>[表级完整性约束条件]],
[<列名><数据类型>[表级完整性约束条件]]......**

>**实例**

```SQL
CREATE TABLE student(Sno CHAR(9) PRIMARY KEY,
Sname CHAR(20) UNIQUE,
Ssex CHAR(2),
Sage SMALLINT,
Sdept CHAR(20));
```
>**其中`student`是表名，`Sno`,`Sname`,`Ssex`,`Sage`,`Sdept`都是列名，后面的`CHAR`都是数据类型,这里的`PRIMARY KEY`是将`Sno`定义为主键,`UNIQUE`是将`Sname`定义为唯一的也就是后面插入数据的时候不能有重复的名字**
>
>**拓展**：主键的定义是在多个候选码中找出那个能够唯一识别一组数据的列名，如果需要两个列名才能识别一组数据，那么可以将这两个列名都定义为主键：`PRIMARY KEY(Sno,Sname)`


## 删除表
>* `DROP TABLE NAME;`只能删除没有被其他表引用，或者没有建立视图的，这里的引用可以是作为被参照表或者作为参照表
>* `DROP TABLE NAME CASCADE`;将全部删除，包括基本表和视图

## 修改表
>### **添加列**
>>**`alter table 表名 add 列名 列数据类型 [after 插入位置]`**

>**例子**
>
>> * `alter table student add grade smallint;` //将grade插入到student表中的末尾一列，这里不加after默认的是在末尾添加
>> * `alter table studnet add grade smallint after Sname;` //这里将grade插入到表中Sname列的后面

>### 删除列
>**`alter table 表名 drop 列名`**
>
>`alter table student drop Sname`;   //输出Sname那一列

>### 修改列
>**`alter table 表名 change 列名称 列新名称 新数据类型;`**

>**实例**

>* alter table student change Sname name char(10) not null;    //修改列名Sname为name,并且还可以修改其中的数据类型，如果想要保持不变，就保持原型。

>### 重命名表
>**`alter table 表名 rename 新表名;`**

>**实例：**
>`alter table student rename STUDENT;`   //将表名改为STUDENT 

## 插入数据
> **`INSERT INTO table_name(列名,列名，列名....)VALUES(DATA);`**   //这里的data一定要对应每一列的数据类型，当然如果要想要插入所有的数据，就不需要列出所有的列名了

>**例子:**
>>* `INSERT INTO student(Sno,Sname,Sage,Ssex)values('201215124','jack',34,'男');`   //这里是插入表中的一些列的数据，并且对应了数据类型
>>* `INSERT INTO student values('201215124','男','jack',34,'IS');`     //这里是按照表中的列名顺序插入数据的

## 更新数据
>**`update 表名称 set 列名称=新值 where 更新条件;`**


>**实例：**
>>* `update student set Sage=Sage+1 where Sno='12134'; `          //将Sno为12134的那一列数据的年龄加一

## 删除表中的数据
>**`delete from 表名称 where 删除条件;`**


>**实例：**
>>* delete from student where Sno='121314125';                //删除Sno为121314125的那一行数据



















































































>>## 作者说
>>> 本人秉着方便他人的想法才开始写技术文章的，因为对于自学的人来说想要找到系统的学习教程很困难，这一点我深有体会，我也是在不断的摸索中才小有所成，如果你们觉得我写的不错就帮我推广一下，让更多的人看到。另外如果有什么错误的地方也要及时联系我，方便我改进，谢谢大家对我的支持

*版权信息所有者：chenjiabing*
*如若转载请标明出处：chenjiabing666.github.io6*