---
title: java连接mysql初探篇
date: 2017-04-16 14:13:04
categories: java学习
tags: JDBC
---

# java连接mysql

## 基本连接

>* **加载驱动:** `Class.forName(com.mysql.jdbc.Driver)`

>* **建立连接:`Connection conn=DriverManager.getConnection(url,user,password)`**
>>其中`url="jdbc:mysql://localhost:3306/java_demo"`,这里的`java_demo`是自己创建的数据库的名字,`user`是`mysql`数据库的管理员，`password`是密码
>**下面直接连接数据库，返回的是接口Connection对象**
```java
import java.sql.*;
public static Connection getConnection()
{
    Connection conn;
    String driver="com.mysql.jdbc.Driver";   //驱动名称
    String url="jdbc:mysql://localhost:3306/java_demo";   //url
    String user="root";
    String password="root";    //管理员和密码都是root
    try{
        Class.forName(driver);    //加载驱动，但是会有ClassNotFoundException异常，因此要避免异常
        try{
            conn = Dri verManager.getConnection(url, user, password);   //获得数据库连接
            return conn;    //返回conn
             
        }catch(SQLException e)
        {
            e.printStackTrace();
        }
    
        
    }catch (ClassNotFoundException e) {
            e.printStackTrace();
    }
    return null;  //如果出现异常就会返回null
    
}
```

## 查询数据

>* **首先根据所得的`Connection`对象创建`Statement`对象：`Statement statement = connection.createStatement()`;

>* **写查询语句：`String sql="select * from student;" `这里是查询所有student中的数据，详细内容请看我的[SQL干货篇二](https://chenjiabing666.github.io/2017/04/09/SQL%E5%B9%B2%E8%B4%A7%E7%AF%87%E4%BA%8C/)**

>* **创建ResultSet对象存储查询结果:`ResultSet res=statement.executeQuery(sql)`,详细的内容请看[官方文档ResultSet详细用法](http://tool.oschina.net/uploads/apidocs/jdk-zh/java/sql/ResultSet.html)**

**代码**
```java
    String sql="select * from student";
    if(!conn.isClosed())
    {
        Statement statement=conn.createStatement();   //这里的conn是上面连接数据库的返回的Connection对象
        ResultSet res=statement.executeQuery(sql);   //执行查询，注意这里只能是executeQuery，Statement还有一些执行mysql函数，但是都不适合查询，后面会详细说
        while(res.next())    //如果res结果中还有元素，那么返回true，否则返回的是false,用来判断是否res中还有结果
        {
            int id=res.getInt("id");    //得到id,这里的id是student表中的属性名 对应的时int BigInt smallint.....
            String name=res.getString("name");  //得到姓名，对应的是mysql中的Char varChar类型
        }
    }
```




>**当然上面只是对于基本的查询数据，在一些项目中根本用不到，因为不太灵活，上面的方法只适合全局查询，并不适合在项目中根据条件查询，下面介绍预编译sql语句的接口[PrepareStatement](http://tool.oschina.net/uploads/apidocs/jdk-zh/java/sql/PreparedStatement.html)**

>* **首先编写sql语句:`sql="select * from student where id=?;";`,这里的`?`表示一个占位，将条件在后面给出，但是这里一定要用`?`**

>* **创建对象：`PrepareStatement pre=conn.preparestatement(sql);`这里传入参数`sql`**

>* **设置`sql`中的条件语句，填补占位`?`的值:pre.setInt(1,1);这里的`SetInt`设置`id`值的为1，因为这的`id`是`int`类型的，第一个参数是表示`prepareindex`，就是表示第一个占位`?`,当然第二个就是2,其中还有`SetString(prepareindex String var)`,用来给定表中的`char`后者`varchar`类型的值**

>**代码：**
```java
if(!connection.isClosed())
                {
                    String sql="select * from course where id=?,name=?";      
                    PreparedStatement preparedStatement=connection.prepareStatement(sql);
                    preparedStatement.setInt(1,1);    //给定条件中的值
                    prepareStatement.setString(2,"jack");  //为第二个？赋值
                    ResultSet res=preparedStatement.executeQuery();    //执行查询，返回的仍然是ResultSet对象
                    while(res.next())
                    {
                        int id=res.getInt("id");
                        String name=res.getString("name");
                        System.out.println(id+"--"+name);
                    }
                }
```

## 插入数据

>**插入数据和上面的两种方法基本是一样的，不同的是`mysql`语句不同，还有的就是执行语句改成了`executeUpdate(sql)`，下面的代码值给出了预编译对象的方法，另外一种的方法使用范围并不是很大，只要把上面的查询改为`executeUpdate`即可**

>**代码：**
```java
 public static int save(MemoBean memo) {
        String sql = "insert into student (username, title, content, momotype, memotime) values (?, ?, ?, ?, ?);";
        Connection conn = getConnection();
        PreparedStatement ps = null;
        try {
            ps = conn.prepareStatement(sql);
            ps.setString(1, memo.getUsername());     //设值value中的值 
            ps.setString(2, memo.getTitle());
            ps.setString(3, memo.getContent());
            ps.setString(4, memo.getMemotype());
            ps.setString(5, memo.getMemotime());
            return ps.executeUpdate();     //这里使用的是excuteUpdate
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            if (ps != null) {
                try {
                    ps.close();   //关闭预编译对象
                } catch (SQLException e) { 
                    e.printStackTrace();   
                }
            }
            if (conn != null) {
                try {
                    conn.close();       //关闭Connection对象
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        } 
        return -1;          //没有插入成功返回-1
    }

```


## 更新数据

>**这里是同样的思路，和插入的基本是一样，只需要改变sql语句即可**

>**代码：**
```java
public static int update(MemoBean memo) {
        String sql = "update student set username=?,title=?,content=?,momotype=?,memotime=? where id=?;";//查询语句
        Connection connection = getConnection();
        PreparedStatement ps = null;
        try {
            ps = connection.prepareStatement(sql);
            ps.setString(1, memo.getUsername());    //设置条件语句中的值
            ps.setString(2, memo.getTitle()); 
            ps.setString(3, memo.getContent());
            ps.setString(4, memo.getMemotype());
            ps.setString(5, memo.getMemotime());
            ps.setInt(6,memo.getId());
            return ps.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
        finally {
            if(ps!=null)
            {
                try {
                    ps.close();
                }catch (SQLException e)
                {
                    e.printStackTrace();
                }
            }
            if(connection!=null)
            {
                try {
                    connection.close();
                }catch (SQLException e)
                {
                    e.printStackTrace();
                }
            }
        }
        return -1;
    }

```

## 最后说
>* **上面的代码是从自己项目中截取的一部分代码，这个是比较适用于面向对象的，也是最常用的对于目前来看**

>* **上面只是给出了查询，插入，更新，因为这是最常用到的方法，其中还有创建表，删除表，当然还有一些他的，这里的创建表直接用`execute(sql)`即可执行，删除表也是用`execute(sql)`即可执行，当然如果要按照指定的条件删除，那么可以使用预编译对象执行**

>* **其中`executeUpdate(sql)`适用于`create`,`insert`,`update`,`delete`,但是`executeQuery(sql)`适用于`select`,具体见官方文档**










































>>## 作者说
>>> 本人秉着方便他人的想法才开始写技术文章的，因为对于自学的人来说想要找到系统的学习教程很困难，这一点我深有体会，我也是在不断的摸索中才小有所成，如果你们觉得我写的不错就帮我推广一下，让更多的人看到。另外如果有什么错误的地方也要及时联系我，方便我改进，谢谢大家对我的支持

*版权信息所有者：chenjiabing*
*如若转载请标明出处：chenjiabing666.github.io6*