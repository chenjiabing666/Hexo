---
title: 线上Bug无法复现怎么办？老司机教你一招，SpringBoot远程调试不用愁！
date: 2020-04-28 11:03:37
categories: 杂谈
tags: 杂谈
---
## 前言

- 在部署线上项目时，相信大家都会遇到一个问题，线上的 Bug 但是在本地不会复现，多么无奈。
- 此时最常用的就是取到前端传递的数据用接口测试工具测试，比如 POSTMAN，复杂不，难受不？
- 今天陈某教你一招，让你轻松调试线上的 Bug。文章目录如下：
  ![](https://gitee.com/chenjiabing666/Blog-file/raw/master/%E8%BF%9C%E7%A8%8B%E8%B0%83%E8%AF%95/7.png)

## 什么是 JPDA？

- `JPDA`(Java Platform Debugger Architecture)，即 Java 平台调试体系，具体结构图如下图所示。
  ![](https://gitee.com/chenjiabing666/Blog-file/raw/master/%E8%BF%9C%E7%A8%8B%E8%B0%83%E8%AF%95/1.png)

- 其中实现调试功能的主要协议是`JDWP`协议，在 `Java SE 5` 以前版本，JVM 端的实现接口是 `JVMPI`(Java Virtual Machine Profiler Interface)，而在 `Java SE 5` 及以后版本，使用 `JVMTI`(Java Virtual Machine Tool Interface) 来替代 JVMPI。
- 因此，如果使用 Java SE 5 之前版本，使用调试功能的命令为：

```java
java -Xdebug -Xrunjdwp:...
```

- 而 `Java SE 5` 及之后版本，使用调试功能的命令为：

```java
java -agentlib:jdwp=...
```

## 调试命令

- 现在开发中最常见的一条远程调试的的命令如下：

```java
java -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=9091 -jar xxx.jar
```

## 参数说明

- 基于前面的调试命令，我们来分析一下基本的参数代表什么意思。

### transport

- 指定运行的被调试应用和调试者之间的通信协议，它由几个可选值：
  1. `dt_socket`：主要的方式，采用`socket`方式连接。
  2. `dt_shmem`：采用共享内存方式连接，仅支持 Windows 平台。

### server

- 指定当前应用作为调试服务端还是客户端，默认为`n`。
- 如果你想将当前应用作为被调试应用，设置该值为 `y`,如果你想将当前应用作为客户端，作为调试的发起者，设置该值为`n`。

### suspend

- 当前应用启动后，是否阻塞应用直到被连接，默认值为 `y`。
- 在大部分的应用场景，这个值为 `n`，即不需要应用阻塞等待连接。一个可能为 `y`的应用场景是，你的程序在启动时出现了一个故障，为了调试，必须等到调试方连接上来后程序再启动。

### address

- 暴露的调试连接端口，默认值为 `8000`。
- **此端口一定不能与项目端口重复，且必须是服务器开放的端口。**

### onthrow

- 当程序抛出设定异常时，中断调试。

### onuncaught

- 当程序抛出未捕获异常时，是否中断调试，默认值为 n。

### launch

- 当调试中断时，执行的程序。

### timeout

- 该参数限定为`java -agentlib:jdwp=…`可用，单位为毫秒`ms`。
- 当 `suspend = y` 时，该值表示等待连接的超时；当 `suspend = n` 时，该值表示连接后的使用超时。

## 参考命令

1. `-agentlib:jdwp=transport=dt_socket,server=y,address=8000`：以 Socket 方式监听 8000 端口，程序启动阻塞（suspend 的默认值为 y）直到被连接。

2. `-agentlib:jdwp=transport=dt_socket,server=y,address=localhost:8000,timeout=5000`：以 Socket 方式监听 8000 端口，当程序启动后 5 秒无调试者连接的话终止，程序启动阻塞（suspend 的默认值为 y）直到被连接。

3. `-agentlib:jdwp=transport=dt_shmem,server=y,suspend=n`：选择可用的共享内存连接地址并使用 stdout 打印，程序启动不阻塞。

4. `-agentlib:jdwp=transport=dt_socket,address=myhost:8000`：以 socket 方式连接到 `myhost:8000`上的调试程序，在连接成功前启动阻塞。

5. `-agentlib:jdwp=transport=dt_socket,server=y,address=8000,onthrow=java.io.IOException,launch=/usr/local/bin/debugstub`：以 Socket 方式监听 8000 端口，程序启动阻塞（suspend 的默认值为 y）直到被连接。当抛出 IOException 时中断调试，转而执行 `usr/local/bin/debugstub`程序。

### IDEA 远程调试示例

- 首先打包 SpringBoot 项目，在服务器上运行，执行以下命令：

```java
java -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=9190 -jar debug-demo.jar
```

- 出现下图的界面，表示运行成功：
  ![](https://gitee.com/chenjiabing666/Blog-file/raw/master/%E8%BF%9C%E7%A8%8B%E8%B0%83%E8%AF%95/2.png)

- 然后在 IDEA 中，点击 `Edit Configurations`，在弹框中点击 `+` 号，然后选择`Remote`。
  ![](https://gitee.com/chenjiabing666/Blog-file/raw/master/%E8%BF%9C%E7%A8%8B%E8%B0%83%E8%AF%95/3.png)
- 填写服务器的地址及端口，点击 OK 即可。
  ![](https://gitee.com/chenjiabing666/Blog-file/raw/master/%E8%BF%9C%E7%A8%8B%E8%B0%83%E8%AF%95/4.png)
- 配置完毕后，DEBUG 调试运行即可。
  ![](https://gitee.com/chenjiabing666/Blog-file/raw/master/%E8%BF%9C%E7%A8%8B%E8%B0%83%E8%AF%95/5.png)
- 配置完毕后点击保存即可，因为我配置的 suspend=n，因此服务端程序无需阻塞等待我们的连接。我们点击 IDEA 调试按钮，当我访问某一接口时，能够正常调试。
  ![](https://gitee.com/chenjiabing666/Blog-file/raw/master/%E8%BF%9C%E7%A8%8B%E8%B0%83%E8%AF%95/6.png)

## 小福利

- 作者为大家准备了接近 10M 的面试题，涵盖后端各个技术维度，老规矩，公众号内回复关键词`JAVA面试题`即可免费获取。
  ![](https://gitee.com/chenjiabing666/Blog-file/raw/master/%E8%BF%9C%E7%A8%8B%E8%B0%83%E8%AF%95/8.png)
- 关注微信公众号回复关键词：
![](https://gitee.com/chenjiabing666/Blog-file/raw/master/%E4%B8%83%E7%A7%92%E7%BC%96%E7%A8%8B.jpg)

