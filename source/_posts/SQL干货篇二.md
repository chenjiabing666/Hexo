---
title: SQL干货篇二
date: 2017-04-09 23:13:13
categories: 数据库干货篇
tags: SQL
---
# SQL干货篇之查询数据
## 单表查询
>**只在一个表中查询数据**
```sql
SELECT * FROM student where Sno='13143';   //根据学号查询数据
```
## 多表查询
>**同时查询多个表**
```sql
SELECT student.Sno,student.Sname,Grade
FROM student,SC where Grade>=90;
```
>**说明：这是在学生表student和成绩表SC中查询成绩大于90的学生姓名和学号,因为这里Sno,Sname在两个表中都存在，因此要指定查询哪一个表中的数据，而Grade只在SC表中出现，因此不用指明哪张表**

## 模糊查找

>**说明：模糊查找用`like`和`not like`进行查找**
* `SELECT * FROM student where Sname like '刘%';`查询所有姓刘的学生
* `SELECT * FROM student where Sname like '%加%'`查询名字中含有加字的学生信息，不固定加字的位置，在任意位置都能查到，这里一般搜索引擎都用是用这种模糊查找的方法来匹配搜索项
* `SELECT * FROM student where Sname like '欧阳_';`查找以姓欧阳并且名字为**三**个字的学生信息
* `SELECT * FROM student WHERE Sname like '_阳';`查找所有姓名为两个字并且第二个字为**阳**的学生信息
* `SELECT * FROM student where Sname like '_阳%';`查询所有姓名中第二个字为**阳**的学生信息
* `SELECT * FROM Course where Cname like '\_IS' ESCAPE '\';`查询课程名字为**_is**的课程信息，这里如果要查询的字符串本省就含有通配符"`%`"或者"`_`"，这时就要使用`ESCAPE<转码字符>`来对字符进行转义了，这里的转码字符可以是任意的，但是我们通常都是用`\`,上面的查询语句中的`\`就是转码字符

## 空值查询
>**判断数据是否为空用`is not null`和`is null`**
`SELECT * FROM student where Sname is null`;查询姓名为空的学生信息

## 多重条件的查询
>**多重条件的查询用AND和OR,其区别不用多说了**
`SELECT Sname FROM student where Sdept='IS' and Sage>20;`查找院系是IS并且年龄超过20岁的学生姓名

## ORDER BY子句(排序)
>**用户可以使用ORDER BY子句对数据进行升序(`ASC`)或者降序(`DESC`)排列**
* `SELECT * FROM student where Sage>20 ORDER BY Sno DESC;`查询年龄超过20岁的学生信息，并且按照降序排列输出
* `SELECT * FROM student ORDER BY Sdept,Sage DESC;`查询全体学生情况，查询结果按照所在系的系号升序排列，同一系的按照年龄降序排列

## 聚集函数
>* `COUNT(*)`   统计元组个数
>* `COUNT(DISTINCT|ALL <列名>)`  计算一列中值的个数，其中DISTINCT表示去除重复的元素，ALL则保留所有的元素
>* `SUM(DISTINCT|ALL <列名>)`   计算一列值的总和
>* `AVG(DISTINCT|ALL<列名>)`     计算一列中的平均值
>* `MAX(DISTINCT|ALL<列名> )`    求一列中的最大值
>* `MIN(DISTINCT|ALL<列名> )`    求一列中的最小值

**实例：**
>* `select count(*) from student;`     查询学生总数
>* `select count(DISTINCT Sdept);`     查询总共有多少系
>* `select AVG(Grade) from SC; `        查询学生的平均分
>* `select SUM(Grade) from SC;`         查询学生的总分
>* `select MAX(Grade) from SC where Cno='1';`    查询课程1的最高分
>* `select MIN(Grade) from SC where Cno='1';`     查询课程1的最低分

**注意：`where`子句中不能用聚集函数，只有在`select`子句和`Group by`子句中才能使用聚集函数**

## GROUP BY子句
* **GROUP BY子句将查询结果按某一列或者多列的值分组，值相等的为一组。**
* **对查询结果分组的目的是为了细化聚集函数的对象。如果未对查询结果进行分组，那么聚集函数将会作用于整个查询结果，分组后聚集函数将会作用于每一组，即每一组都有一个函数值**

>**实例：**
* `select Cno,Count(Sno) from SC Group by Cno;`      求各个课程号以及相应的选课人数
* `select Cno as '课程号',count(Sno) as '选课人数' from sc group by Cno;`求各个课程号以及相应的选课人数
* `select Cno,count(Sno),AVG(Grade) from sc group by Cno Having AVG(Grade)>80;`  查询课程平均分大于80分的课程号和所选学生人数,这里是先分组后然后对这些组进行筛选就用`Having`子句进行条件筛选，**不能使用`where`子句进行筛选**,当然这里的sleect子句中的AVG(Grade)可以去掉,可以写成`select Cno,count(Sno) from sc group by Cno Having AVG(Grade)>80;`
* `select Sno from sc Group by Sno having count(*)>2;`   查询选修了两门以上课程的学生学号
* `select Sno,AVG(Grade) from sc Group by Sno;` 查询每一个学生选修课程的平均成绩,这里先按照学号进行分组，然后对每一个分组进行求平均成绩

>**注意：这里的如果使用了聚集函数，那么select子句中出现的选项一定要在聚集函数或者Group by子句中出现，否则就会出现错误，如：`select Sno,count(Cno) from sc;`这条语句就是错误的，因为`Sno`没有出现在聚集函数或者`Group by`子句中，如果改成`select Sno,count(Cno) from SC Group by Sno;`就正确了,因为Sno出现在了`Group by`子句中了**

## 连接查询
>**如果一个查询涉及两个以上的表则称之为连接查询，连接查询包括等值连接查询，自然连接查询，自身连接查询，非等值连接查询，外连接查询，复合条件查询**

>### 等值和非等值连接查询
>**当连接运算符为=时为等值连接查询，否则为非等值连接查询**

>**实例：**
>* `select student.*,SC.* from student,SC where student.Sno=SC.Sno;`查询每个学生及其选修课程的情况

>### 自然连接查询
>**在等值连接的基础上去掉相等的属性组就是自然连接查询**

>**实例：**
>`select student.Sno,Sname,Ssex,Sage,Sdept,Cno,Grade from student,SC where student.Sno=SC.Sno; `


## 嵌套查询
>**在`SQL`语言中一个`SELECT-FROM-WHERE`语句称为一个**查询块**，将一个查询块嵌套在另外一个查询块的`WHERE`子句或`HAVING`短语的条件查询称之为嵌套查询**
>**实例：**
```sql
SELECT SNAME FROM STUDENT WHERE SNO IN    /*外层查询*/
(SELECT SNO FROM SC WHERE CNO='2');    /*内层查询或者子查询*/
```
>**注意:**
>* 这里的查询条件Sno只能有一个，并且外层查询的where子句中出现的Sno属性要和内层查询select语句中的Sno属性要对应。
>* 子查询中不能使用`ORDER BY`子句，`ORDER BY`子句只能对最终的查询结果排序

### 带有IN谓词的嵌套查询
>**实例：**

```sql
SELECT Sno,Sname,Sdept from student where Sdept IN
(SELECT Sdept From student Where Sname='刘晨');
```  

**查询与刘晨在同一个系的学生信息,当然本例中也可以用自身连接查询来完成，如下：**

```sql
select first.Sno,first.Sname,first.Sdept 
from student first,student second
where first.Sdept=second.Sdept and second.Sname='刘晨';
```

### 带有比较运算符的子查询
```sql
select Sno,Cno from sc x where Grade >
(select AVG(Grade) from sc y where x.Sno=y.Sno);
```
**查询了所有学生成绩超过选修课程平均成绩的课程号**

### 带有ANY或者ALL的谓词子查询
>**`ANY`表示查询条件只要满足其中一个即可，而`ALL`表示查询条件要满足所有的才行**
>**实例：**
> * `SELECT SNAME,SAGE FROM STUDENT WHERE SAGE<ANY(SELECT SAGE FROM STUDENT WHERE SDEPT='CS') AND SDEPT!='CS';`查询非计算机系的比计算机系**任意**一个学生年龄小的学生姓名和年龄,这里只要满足比一个学生的年龄小即可
>* `SELECT SNAME,SAGE FROM STUDENT WHERE SAGE<ALL(SELECT SAGE FROM STUDENT WHERE SDEPT='CS') AND SDEPT!='CS';`查询非计算机系的比计算机系的所有学生年龄小的学生信息，这里要满足比所有的学生信息都要小，**就是比计算机系年龄最小的都要小**


### 带有EXISTS谓词的子查询
>**`EXISTS`表示存在的意思，带有`EXISTS`的子查询步返回任何的数据，只产生逻辑真或者假**
>* `SELECT Sname From student where EXISTS (SELECT Sname from SC where Sno=student.Sno and Cno='2');`查询选择课程2的学生姓名，这里只判断是否存在这样的学生，如果子查询中没有找到课程2这项，那么查到的就是空,子查询只判断是否为true or false,当然还有`NOT EXISTS`


## 集合查询
>**集合操作包括并操作`UNION`、交操作`INTERSECT`、差操作`EXCEPT`**

>**实例：**
>* `select * from student where Sdept= 'CS' UNION select * from student where Sage>19;`查找计算机系的学生以及年龄不大于19岁的学生信息，这里`UNION`会自动去掉重复的元组，如果想要保留**全部**的数据需要用`UNION ALL`
>* `select Sno from SC where Cno='1' UNION select Sno from SC where Cno='2';`查询选修课程1或者选修课程2的学生学号，这里并集就是去掉重复的元组，使用`UNION ALL` 可以保留
>* `select Sno from SC where Cno='1' Intersect select Sno from SC where Cno='2';`查询同时选修课程1和课程2的学生学号

### 基于派生表的查询
```sql
select Sno,Cno from SC,(select Sno,AVG(Grade) from SC Group by Sno) 
AS AVG_SC(avg_Sno,avg_grade)
where SC.Sno=AVG_SC.avg_Sno and SC.Grade>=AVG_SC.avg_grade;
```
>**这里的From子句中将会派生出一个AVG_SC表,该表由avg_Sno、avg_grade组成，主查询将SC表和AVG_SC表进行连接，选出修课成绩大于其平均成绩的课程号**

>**注意：如果子查询中没有聚集函数，那么派生表不用指定属性列，子查询后面的列名为其属性，如下：**
>>`select Sname from student,(select Sno From SC where Cno='1') AS SCI where student.Sno=SCI.Sno;`这里的SCI默认的列属性名是Sno，AS关键词可以省略，但是必须要为派生表指定一个别名。






































>>## 作者说
>>> 本人秉着方便他人的想法才开始写技术文章的，因为对于自学的人来说想要找到系统的学习教程很困难，这一点我深有体会，我也是在不断的摸索中才小有所成，如果你们觉得我写的不错就帮我推广一下，让更多的人看到。另外如果有什么错误的地方也要及时联系我，方便我改进，谢谢大家对我的支持

*版权信息所有者：chenjiabing*
*如若转载请标明出处：chenjiabing666.github.io6*