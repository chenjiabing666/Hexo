---
title: MongoDB干货篇之安装
date: 2017-04-28 10:53:32
categories: 数据库干货篇
tags: MongoDB
---

# MongoDB干货篇之安装

## 安装

>* **[下载地址](http://www.mongodb.org/downloads)**

>* **点击安装,选择自定义，后选择安装路径，不过最好安装在根目录下(`C盘`)，然后点解`next`,这里我安装的路径是`C:\MongoDB`**

>* **创建文件夹:在`C:\MongoDB`下创建一个文件夹`data`,然后在`data`文件夹下创建`db`,`log`两个子文件夹,在`log`文件下创建一个`MongoDB.log`文档，总得来说创建了`C:\MongoDB\data`,`C:\MongoDB\data\db`,`C:\MongoDB\data\log`,`C:\MongoDB\data\log\MongoDB.log`**

>* **在`C:\MongoDB\bin`文件夹下运行`cmd.exe`进入`dos`命令，执行以下命令：**
>> * **然后在`cmd`下输入`mongod -dbpath "C:\MongoDB\data\db`,将会看到一些信息，说明已经安装成功了**

## 测试连接

>* **在`C:\MongoDB\bin`文件夹下运行`cmd.exe`,输入`mongo`或者`mongo.exe`,将会出现连接的信息，说明已经连接成功了**

>* **然后在另外一个`cmd.exe`在`bin`目录下运行`mongo`可以看到已经连接上`MongoDB`了，注意上面打开的终端不能关闭，否则不能成功连接，这是比较麻烦的，需要每次连接都要启动，下面我们需要把它安装为`windows`服务**

## 安装程windows服务
**注意在管理员的`cmd.exe`中运行以下命令，否则在`MongoDB.log`文件里出现遭到拒绝**

>* **运行`cmd`，进入`bin`目录，执行以下命令:**
>>* **`mongod --dbpath "C:\MongoDB\data\db" --logpath "D:\MongoDB\data\log\MongoDB.log" --install --serviceName "MongoDB"`,这里的服务名为`MongoDB`，可以在`C:\MongoDB\data\log\MongoDB.log`文件里查看相关信息，如果出现遭到拒绝就是没有在管理员的权限下执行命令**

>* **接下来就是启动服务了，现在在`cmd.exe`中运行`NET START MongoDB`，如果看到服务成功启动，那么就成功了，但是我在启动的时候出现`48`错误，下面将会做出解决方法：**
>> * **先删除服务:`mongod --dbpath "C:\MongoDB\data\db" --logpath "C:\MongoDB\data\log\MongoDB.log" --remove --serviceName "MongoDB"`**
>> * **删除`MongoDB`目录下的`mongod.lock`**
>> * **然后就是重新安装了,执行以下命令：**
>>>* **`mongod --logpath "C:\MongoDB\data\log\MongoDB.log" --logappend --dbpath "C:\Mongodb\data" --directoryperdb --serviceName "MongoDB" --serviceDisplayName "MongoDB" --install`**
>> * **接下来重新启动服务，`net start MongoDB`,可以看到成功启动了**






>>## 作者说
>>> 本人秉着方便他人的想法才开始写技术文章的，因为对于自学的人来说想要找到系统的学习教程很困难，这一点我深有体会，我也是在不断的摸索中才小有所成，如果你们觉得我写的不错就帮我推广一下，让更多的人看到。另外如果有什么错误的地方也要及时联系我，方便我改进，谢谢大家对我的支持

*版权信息所有者：chenjiabing*
*如若转载请标明出处：chenjiabing666.github.io6*