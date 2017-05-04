---
title: SQL干货篇三
date: 2017-04-16 22:36:33
categories: 数据库干货篇
tags: SQL
---
# SQL干货篇三

## 创建视图
>* **`create view <视图名>[(列名),(列名)...] as <子查询> [with check option]`**
>* **子查询可以是select语句或者包含order by子句，具体情况而定，视图名是一定要有的，列名可以省略，如果省略的话则是由子查询中的目标列的相关字段组成，当然也可以自己指定，with check option表示如果视图或者参照表执行insert,update,delete时，那么视图或者参照表会随着变化，也就是两个绑定在一起的意思，当然也可以选择不用，那么视图的增删改就和参照表没有关系了**
#### 实例
>**建立在一个表上**
```sql
create view IS_student as select Sno,Sname,Sage where Sdept='IS'
with check option ;   /*将所有的IS系的学生学号建立一个视图IS_student,其中的列名是Sno,Sname,Sage*/
```

>**建立在多个表上**
```sql
create view IS_Grade(Sno,Sname,Grade) 
as select student.Sno,Sname,Grade from student,SC
where Sdept='IS' and student.Sno=SC.Sno;           /*建立在两个表上的视图，可以看出这里已经指出指定的列名，但是这个列名并不是固定的，可以根据具体的含义来指定*/
```

>**定义一个带有表达式的视图**
```sql
create view BT_S(Sno,Sname,Sbirth) 
as select Sno,Sname,2014-Sage from student    /*这里的2014-Sage是用来计算出生日期的*/
with check option;
```

>**聚集函数的视图**
```sql=
create view BT(Sno,Gavg) 
as select Sno,AVG(Grade) from SC Group by Sno;  /*这里的AVG(Grade)是用来计算平均成绩的，Group by是用来根据学号分组，这里就是求同一个人的多门学科的平均成绩*/
```

## 删除视图
>* **`Drop view <视图名><CASCADE]`,这里的CASCADE表示如果还导出了其他的视图，那么加上CASCADE就会全部删除**

#### 实例
>* `Drop view IS_Sdept;` 删除视图
>* `Drop view IS_Sdept CASCADE;`  删除视图和其导出视图


## 查询视图
>**查询视图和查询表是一样的，请参照我前两章讲的[SQL语法](https://chenjiabing666.github.io/2017/04/09/SQL%E5%B9%B2%E8%B4%A7%E7%AF%87%E4%BA%8C/)**


## 更新视图
>**视图的更新包括insert,delete,update,这个和基本表的操作是一样的**

>**注意：**
>>* 并不是所有的视图都可以更新的,比如上面根据学生多科平均成绩建立的视图，这里如果将视图中的平均成绩更新了，那么参照表的数据就不能对应的更新了，这就会不允许更新，当然这是在添加了`with check option`语句的情况下
>>* 如果添加了`with check option`语句,那么对视图的更新就会对应转换成对基本表的更新
>>* 各个系统对视图的更新还有进一步的规定，比如DB2规定：
>>>* 如果视图是由两个以上的基本表导出，那么就不可以更新
>>>* 如果视图来自字段或者表达式，那么就不允许对此视图执行`insert`,`update`,但是可以执行`delete`
>>>* 如果定义中有order by子句，那么不可以更新视图












>>## 作者说
>>> 本人秉着方便他人的想法才开始写技术文章的，因为对于自学的人来说想要找到系统的学习教程很困难，这一点我深有体会，我也是在不断的摸索中才小有所成，如果你们觉得我写的不错就帮我推广一下，让更多的人看到。另外如果有什么错误的地方也要及时联系我，方便我改进，谢谢大家对我的支持

*版权信息所有者：chenjiabing*
*如若转载请标明出处：chenjiabing666.github.io6*