title: Mybatis Log plugin破解，亲测可用！！！
date: 2020-08-26 14:50:11
categories: 开发工具
tags: 破解
---
## 前言

- 今天重新装了IDEA2020，顺带重装了一些插件，毕竟这些插件都是习惯一直在用，其中一款就是Mybatis  Log plugin，按照往常的思路，在IDEA插件市场搜索安装，艹，眼睛一瞟，竟然收费了，对于我这种支持盗版的人来说太难了，于是自己开始捣鼓各种尝试破解，下文分享自己的破解方式。

## 什么是Mybatis Log plugin

- 举个栗子，通常在找bug的时候都会查看执行了什么SQL，想把这条SQL拼接出来执行调试，可能有些小白还在傻傻的把各个参数复制出来，补到`?`占位符中，哈哈。
- ![](https://gitee.com/chenjiabing666/Blog-file/raw/master/Mybatis-Log-plugin/6.jpg)



- 简单的说就是能根据log4j的打印的sql日志一键生成执行的`sql`语句。
- 类似如下一个日志信息：
- ![日志信息](https://gitee.com/chenjiabing666/Blog-file/raw/master/Mybatis-Log-plugin/1.png)
- 如果使用Log plugin这个插件，将会很容易的把参数添加到sql语句中得到一条完整的sql，效果如下：
- ![完整sql](https://gitee.com/chenjiabing666/Blog-file/raw/master/Mybatis-Log-plugin/4.png)
- 一旦开启了`Mybatis Log plugin`这个插件，在程序运行过程中只要是有SQL语句都会自动生成在`Mybatis Log`这个界面，当然也可以自己关掉。

## 如何安装

- `Setting->plugin->Marketplace`搜索框输入`Mybatis Log plugin`，如下：
- ![搜索输入](https://gitee.com/chenjiabing666/Blog-file/raw/master/Mybatis-Log-plugin/2.png)
- 很遗憾的是， IDEA2020中已经开始收费了，艹，对于一向支持盗版的我来说，很不爽~



## 如何破解

- 下载jar包`plugin.intellij.assistant.mybaitslog-2020.1-1.0.3.jar`，文末附有下载方式。
- `setting->plguin->设置-> install plugin from Disk...`

![破解步骤](https://gitee.com/chenjiabing666/Blog-file/raw/master/Mybatis-Log-plugin/3.png)



## 如何使用

- 日志中从`Preparing`到`Parameters`这两行的参数选中，右键选择`restore sql from Selection`
- ![](https://gitee.com/chenjiabing666/Blog-file/raw/master/Mybatis-Log-plugin/5.png)
- 此时将会在`Mybatis Log`界面出现完整的SQL语句。



## 总结

- 对于复杂的SQL语句来说，Mybatis Log plugin这款插件简直是太爱了，能够自动拼接参数生成执行的SQL语句。
- 老规矩，关注公众号【码猿技术专栏】，公众号回复`mybatis log`获取破解包。
- ![](https://gitee.com/chenjiabing666/Blog-file/raw/master/%E4%B8%83%E7%A7%92%E7%BC%96%E7%A8%8B.jpg)