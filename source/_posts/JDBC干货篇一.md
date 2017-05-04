---
title: JDBC干货篇一
date: 2017-04-27 23:03:51
categories: java学习
tags: JDBC
---
# JDBC干货篇一

## JDBC基础
>* **`JDBC`的全称是`Java Database Connectivity`，即`Java`数据库连接，它是一种可以执行`SQL`语句的`Java API`。程序可通过`JDBC API`连接到关系数据库，并使用结构化查询语言（`SQL`，数据库标准的查询语言）来完成对数据库的查询、更新**

>* **与其他数据库编程环境相比，`JDBC`为数据库开发提供了标准的`API`，使用`JDBC`开发的数据库应用可以跨平台运行，而且还可以跨数据库（如果全部使用标准的`SQL`语句）。也就是说如果使用JDBC开发一个数据库应用，则该应用既可以在Windows操作系统上运行，又可以在`Unix`等其他操作系统上运行，既可以使用`MySQ`L数据库，又可以使用`Oracle`等其他的数据库，应用程序不需要做任何的修改**

## 加载数据库驱动
>* **`Class.forName(classDriver)`其中`classDrive`r就是数据库驱动类对应的字符串,下面给出加载`mysql`,`oracle`数据库的例子：**

```java
Class.forName("com.mysql.jdbc.Driver");   //mysql
Class.forName("oracle.jabc.driver.OracleDriver");    //oracle

```

## 获取数据库连接
**获得数据库连接的方法为`DriverManager.getConnection()`,其中有不同的参数，也对应不同的方法，下面将会详细介绍**
>* **`DriverManager.getConnection(String url)`**   

>* **`DriverManager.getConnection(String url, Properties prop)`  这里的Properties是一个属性集，详情请看[文档](http://tool.oschina.net/uploads/apidocs/jdk-zh/java/util/Properties.html)**

>* **`DriverManager.getConnection(String url,String user,String password)`  这里的`url`是`jdbc:mysql://localhost:3306/java_demo`，其中`java_demo`表示你自己创建的数据库名字，`urser`表示当前数据库的登录的用户名，`password`表示密码**

```java
        //第二种方法
        String url="jdbc:mysql://localhost:3306/java_demo";  //这是连接的url
        String user="root";
        String password="root";   
        Properties properties=new Properties();   //创建属性集
        properties.setProperty("password", password);   //向起中添加属性,很想python中的字典
        properties.setProperty("user",user);   
        Class.forName("com.mysql.jdbc.Driver");   //加载数据库驱动
        Connection conn=DriverManager.getConnection(url,properties);   //连接数据库
        
        //第三种方法
        
        Connection conn=DriverManager.getConnection(url,user,password);   //连接数据库 
        

```
>**注意：以上只是一些例子，并不是完整的代码，其中并没有处理异常，还应该注意的是要关闭connection**

## 查询数据
>**查询数据有两种方法，分别为静态查询和动态查询，静态查询使用的[Statement](http://tool.oschina.net/uploads/apidocs/jdk-zh/index.html?index-filesindex-16.html)，动态查询使用的[PrepareStatement](http://tool.oschina.net/uploads/apidocs/jdk-zh/index.html?index-filesindex-16.html),下面详细介绍这两种查询方法**

### 静态查询
>**使用的是`Statement`,其中常用的函数如下：**
>* **`boolean execute(String SQL)`  如果`ResultSet`对象可以被检索，则返回的布尔值为` true `，否则返回 `false` 。当你需要使用真正的动态 `SQL` 时，可以使用这个方法来执行 `SQL DDL` 语句**

>* **`int executeUpdate(String SQL)`  返回执行 `SQL` 语句影响的行的数目。使用该方法来执行 `SQL` 语句，是希望得到一些受影响的行的数目，例如，`INSERT`，`UPDATE` 或 `DELETE` 语句**

>* **`ResultSet executeQuery(String SQL) `: 返回一个 `ResultSet` 对象。当你希望得到一个结果集时使用该方法，就像你使用一个 `SELECT` 语句。**

>* **`close()`关闭`statement`对象，这个是必须有的，为了程序的安全，必须在结束之前关闭**

>**实例：**

```java

    Statement stmt = null;   //申请对象
try {
   stmt = connection.createStatement( );   //通过Connection对象创建statement对象
   
   String sql_1="select * from course;";
   String sql_2="select * from course where id=2;";
   
   ResultSet res_1=stm.executeQuery(sql_1);   //执行查询语句，返回的是一个结果集合，上面已经说明了
   ResultSet res_2=stm.executeQuery(sql_1);
   
   while(res_1.next())
   {
   System.out.println(res_1.getInt(1)+"---"+res_1.getString(2));   //分别查询第一列和第二列的值，通过列数查询
   System.out.println(res_1.getInt("id")+"---"+res_1.getString("name"));   //通过列名查询
   
   }
  
   }
catch (SQLException e) {    //捕捉异常
   . . .
}
finally {
    if(connection!=null)
    {
        connection.close();    //关闭连接
    }
   if(stmt!=null)
   {
       stmt.close();  //关闭
   }
}

```

>**说明：`ResultSet`常用的方法如下：注意下面的方法会发生`SQLException`异常**

>* **`public void beforeFirst()` 将光标移动到第一行之前。**

>* **`public void afterLast()`  将光标移动到最后一行之后。**

>* **`public boolean first()`  将光标移动到第一行。从第一行的数据开始读取**

>* **`public void last()` 将光标移动到最后一行。**

>* **`public boolean absolute(int row)` 将光标移动到指定的第` row `行。**

>* **`public boolean previous()` 将光标移动到上一行，如果超过结果集的范围则返回` false`。**

>* **`public boolean next()` 将光标移动到下一行，如果是结果集的最后一行则返回 false。**

>* **`public int getRow()` 返回当前光标指向的行数的值。**

>* **`public void moveToInsertRow()` 将光标移动到结果集中指定的行，可以在数据库中插入新的一行。当前光标位置将被记住**

>* **`public void moveToCurrentRow()` 如果光标处于插入行，则将光标返回到当前行，其他情况下，这个方法不执行任何操作**

>* **`public int getInt(String columnName) `返回当前行中名为 `columnName `的列的 `int` 值。**

>* **`public int getInt(int columnIndex)` 返回当前行中指定列的索引的` int `值。列索引从 `1` 开始，意味着行中的第一列是` 1` ，第二列是 `2` ，以此类推。**

>* **`getString(int columIndex)` 返回指定列的`String`类型的数据**

>* **`getString(String columName)` 返回当前行中名为`columName`的`String`类型的值**

### 动态查询
>**动态查询使用的`PrepareStatement`这个类实现的，`PreparedStatement` 接口扩展了 `Statement` 接口，它让你用一个常用的 `Statement` 对象增加几个高级功能。这个 `statement` 对象可以提供灵活多变的动态参数**

> **实例：**

```java

PreparedStatement pstmt = null;
try {
   String SQL = "select * from course where age=? and name=?";
   pstmt = conn.prepareStatement(SQL);   //创建对象
   pstmt.setInt(1,22);   //设置参数age的值 ，1表示第一个参数
   pstmt.setString(2,"chenjiabing");   //设置name的值，其中2表示第二个参数
   ResultSet res=pstmt.execteQuery();
   while(res.next)
   {
       ....
   }
   
   . . .
}
catch (SQLException e) {
   . . .
}
finally {
    if(connection!=null)
    {
        connection.close();
    }
    if(pstmt!=null)
    {
    pstmt.close();   //关闭
    }

   . . .
}

```

>**说明:`JDBC` 中所有的参数都被用` ? `符号表示，这是已知的参数标记。在执行` SQL` 语句之前，你必须赋予每一个参数确切的数值。其中`PrepareStatement`的常用函数如下，当然`Statement`中的`execute` ,`executeQuery `,`executeUpdate`也可以使用**

>* **`void setInt(int parameterIndex, int x)` `parameterIndex`表示第几个`?`,这里的`int x`表示是`mysql`中定义的`int`类型的值**
>* **`void setString(int parameterIndex,String x)`  为第`parameterIndex`个`String`类型的?赋予`x`的值**


## 其他的操作
>**这里还有`delete`,`update`,`alter`等一系列的操作都是和上面的一样，就是把`sql`语句改变以下，如果使用的是静态的就要为`delete`,`update`,使用`Statement.execteUpdate(sql)`这个函数,当然要使用动态的也是`executeUpdate`函数**



















>>## 作者说
>>> 本人秉着方便他人的想法才开始写技术文章的，因为对于自学的人来说想要找到系统的学习教程很困难，这一点我深有体会，我也是在不断的摸索中才小有所成，如果你们觉得我写的不错就帮我推广一下，让更多的人看到。另外如果有什么错误的地方也要及时联系我，方便我改进，谢谢大家对我的支持

*版权信息所有者：chenjiabing*
*如若转载请标明出处：chenjiabing666.github.io6*