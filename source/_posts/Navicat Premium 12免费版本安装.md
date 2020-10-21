---
title: Navicat Premium 12破解版安装
date: 2020-08-27 15:51:57
categories: 开发工具
tags: 破解
---

## 前言

- 这几年的工作过程中使用了很多的数据库工具，比如Sqlyog，DBeaver,sqlplus等工具，但是个人觉得很好用的还是Navicat。
- 不如人意的就是目前Navicat都在收费，今天就来分享下如何安装免费的Navicat。
- ![](https://gitee.com/chenjiabing666/Blog-file/raw/master/Mybatis-Log-plugin/6.jpg)



## 免费版本安装

1. 首先去官网下载Navicat_12的安装包，根据自己电脑的配置下载合适的。

![](https://gitee.com/chenjiabing666/Blog-file/raw/master/navicat12/1.png)

2. 下载成功之后，直接`安装`，`启动`即可
3. 启动时选择`试用`版本。
4. 打开**神秘的包包**，找到匹配的，如下：

![](https://gitee.com/chenjiabing666/Blog-file/raw/master/navicat12/2.png)

5. 将其中的文件全部复制到`Navicat_12`的根目录，文件如下：

![](https://gitee.com/chenjiabing666/Blog-file/raw/master/navicat12/3.png)

6. 重新启动Navicat，出现以下界面，表示安装成功，如下：

![](https://gitee.com/chenjiabing666/Blog-file/raw/master/navicat12/4.png)



## 如何连接Oracle

- 如果本地未安装过Oracle数据库，新安装的Navicat默认是连接不上oracle的，需要配置一下`oci.dll`。
- 选择`工具`->`选项`
- ![](https://images2018.cnblogs.com/blog/1023171/201808/1023171-20180819144807854-2077107245.png)
- 指定`oci.dll`的路径，如下：
- ![](https://images2018.cnblogs.com/blog/1023171/201808/1023171-20180819144833188-1560669113.png)
- 重新启动，即可连接。
- **注意**：Navicat_12自带的oci.dll如果版本不合适，可以去官网下载对应的版本。

## 如何连接Sql server

- 如果本地未安装过SQL Server数据库，Navicat是不能连接上数据库的，具体解决方案如下：
- 在Navicat的根目录下找到`sqlncli_x64.msi`双击安装即可，当然如果版本不合适，可以自己去官网下载。
- ![](https://gitee.com/chenjiabing666/Blog-file/raw/master/navicat12/5.png)
- 安装成功后，重启启动，即可连接。

## 总结

- 安装免费版的Navicat很简单，只需要一个神秘的包包，哈哈。
- 老规矩，关注公众号【码猿技术专栏】回复关键词`Navicat12`即可获取。
- ![](https://gitee.com/chenjiabing666/Blog-file/raw/master/%E4%B8%83%E7%A7%92%E7%BC%96%E7%A8%8B.jpg)

