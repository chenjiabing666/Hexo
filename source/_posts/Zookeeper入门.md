---
title: Zookeeper入门
date: 2020-04-19 15:48:16
categories: 杂谈
tags: 杂谈
---
## 导读

- Zookeeper 相信大家都听说过，最典型的使用就是作为服务注册中心。今天陈某带大家从零基础入门 Zookeeper，看了本文，你将会对 Zookeeper 有了初步的了解和认识。
- 注意：本文基于 Zookeeper 的版本是 3.4.14，最新版本的在使用上会有一些出入，但是企业现在使用的大部分都是 3.4x 版本的。

## Zookeeper 概述

- Zookeeper 是一个分布式协调服务的开源框架。主要用来解决分布式集群中应用系统的一致性问题，例如怎样避免同时操作同一数据造成脏读的问题。
- ZooKeeper 本质上是一个分布式的小文件存储系统。提供基于类似于文件系 统的目录树方式的数据存储，并且可以对树中的节点进行有效管理。从而用来维护和监控你存储的数据的状态变化。通过监控这些数据状态的变化，从而可以达 到基于数据的集群管理。诸如：`统一命名服务`、`分布式配置管理`、`分布式消息队列`、`分布式锁`、`分布式协调`等功能。

## Zookeeper 特性

1. `全局数据一致`：每个 server 保存一份相同的数据副本，client 无论连 接到哪个 server，展示的数据都是一致的，这是最重要的特征；

2. `可靠性`：如果消息被其中一台服务器接受，那么将被所有的服务器接受。
3. `顺序性`：包括全局有序和偏序两种：全局有序是指如果在一台服务器上 消息 a 在消息 b 前发布，则在所有 Server 上消息 a 都将在消息 b 前被 发布；偏序是指如果一个消息 b 在消息 a 后被同一个发送者发布，a 必将排在 b 前面。

4. `数据更新原子性`：一次数据更新要么成功（半数以上节点成功），要么失 败，不存在中间状态；

5. `实时性`：Zookeeper 保证客户端将在一个时间间隔范围内获得服务器的更新信息，或者服务器失效的信息。

## Zookeeper 节点类型

- Znode 有两种，分别为临时节点和永久节点。
  - `临时节点`：该节点的生命周期依赖于创建它们的会话。一旦会话结束，临时节点将被自动删除，当然可以也可以手动删除。临时节点不允许拥有子节点。
  - `永久节点`：该节点的生命周期不依赖于会话，并且只有在客户端显示执行删除操作的时候，他们才能被删除。
- 节点的类型在创建时即被确定，并且不能改变。
- Znode 还有一个序列化的特性，如果创建的时候指定的话，该 Znode 的名字后面会自动追加一个不断增加的序列号。序列号对于此节点的父节点来说是唯一的，这样便会记录每个子节点创建的先后顺序。它的格式为`"%10d"`(10 位数字,没有数值的数位用 0 补充，例如“0000000001”)。
- 这样便会存在四种类型的 Znode 节点，分类如下：
  - `PERSISTENT`：永久节点
  - `EPHEMERAL`：临时节点
  - `PERSISTENT_SEQUENTIAL`：永久节点、序列化
  - `EPHEMERAL_SEQUENTIAL`：临时节点、序列化

## ZooKeeper Watcher

- ZooKeeper 提供了分布式数据发布/订阅功能，一个典型的发布/订阅模型系统定义了一种一对多的订阅关系，能让多个订阅者同时监听某一个主题对象，当这个主题对象自身状态变化时，会通知所有订阅者，使他们能够做出相应的处理。
- 触发事件种类很多，如：节点创建，节点删除，节点改变，子节点改变等。
- 总的来说可以概括 Watcher 为以下三个过程：客户端向服务端注册 Watcher、服务端事件发生触发 Watcher、客户端回调 Watcher 得到触发事件情况。

## Watcher 机制特点

- `一次性触发` ：事件发生触发监听，一个 watcher event 就会被发送到设置监听的客户端，这种效果是一次性的，后续再次发生同样的事件，不会再次触发。

- `事件封装` ：ZooKeeper 使用 WatchedEvent 对象来封装服务端事件并传递。WatchedEvent 包含了每一个事件的三个基本属性： `通知状态`（keeperState），`事件类型`（EventType）和`节点路径`（path）。

- `event 异步发送` ：watcher 的通知事件从服务端发送到客户端是异步的。

- `先注册再触发` ：Zookeeper 中的 watch 机制，必须客户端先去服务端注册监听，这样事件发送才会触发监听，通知给客户端。

## 常用 Shell 命令

### 新增节点

```bash
create [-s] [-e] path data
```

- `-s`：表示创建有序节点
- `-e`：表示创建临时节点
- 创建持久化节点：

```bash
create /test 1234

## 子节点
create /test/node1 node1
```

- 创建持久化有序节点：

```bash
## 完整的节点名称是a0000000001
create /a a
Created /a0000000001

## 完整的节点名称是b0000000002
create /b b
Created /b0000000002
```

- 创建临时节点：

```bash
create -e /a a
```

- 创建临时有序节点：

```bash
## 完整的节点名称是a0000000001
create -e -s /a a
Created /a0000000001
```

### 更新节点

```bash
set [path] [data] [version]
```

- `path`：节点路径
- `data`：数据
- `version`：版本号
- 修改节点数据：

```bash
set /test aaa

## 修改子节点
set /test/node1 bbb
```

- 基于数据版本号修改，如果修改的节点的版本号(`dataVersion`)不正确，拒绝修改

```bash
set /test aaa 1
```

### 删除节点

```bash
delete [path] [version]
```

- `path`：节点路径
- `version`：版本号，版本号不正确拒绝删除
- 删除节点

```bash
delete /test

## 版本号删除
delete /test 2
```

- 递归删除，删除某个节点及后代

```bash
rmr /test
```

### 查看节点数据和状态

- 命令格式如下：

```bash
get path
```

- 获取节点详情：

```bash
## 获取节点详情
get /node1

## 节点内容
aaa
cZxid = 0x6
ctime = Sun Apr 05 14:50:10 CST 2020
mZxid = 0x6
mtime = Sun Apr 05 14:50:10 CST 2020
pZxid = 0x7
cversion = 1
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 3
numChildren = 1
```

- 节点各个属性对应的含义如下：
  - `cZxid`：数据节点创建时的事务 ID。
  - `ctime`：数据节点创建时间。
  - `mZxid`：数据节点最后一次更新时的事务 ID。
  - `mtime`：数据节点最后一次更新的时间。
  - `pZxid`：数据节点的子节点最后一次被修改时的事务 ID。
  - `cversion`：子节点的更改次数。
  - `dataVersion`：节点数据的更改次数。
  - `aclVersion`  ：节点 ACL 的更改次数。
  - `ephemeralOwner`：如果节点是临时节点，则表示创建该节点的会话的 SessionID。如果节点是持久化节点，值为 0。
  - `dataLength`  ：节点数据内容的长度。
  - `numChildren`：数据节点当前的子节点的个数。

### 查看节点状态

```bash
stat path
```

- `stat`命令和`get`命令相似，不过这个命令不会返回节点的数据，只返回节点的状态属性。

```bash
stat /node1

## 节点状态信息，没有节点数据
cZxid = 0x6
ctime = Sun Apr 05 14:50:10 CST 2020
mZxid = 0x6
mtime = Sun Apr 05 14:50:10 CST 2020
pZxid = 0x7
cversion = 1
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 3
numChildren = 1

```

### 查看节点列表

- 查看节点列表有`ls path`和`ls2 path`两个命令。后者是前者的增强，不仅会返回节点列表还会返回当前节点的状态信息。
- `ls path`：

```bash
ls /

## 仅仅返回节点列表
[zookeeper, node1]
```

- `ls2 path`：

```bash
ls2 /

## 返回节点列表和当前节点的状态信息
[zookeeper, node1]
cZxid = 0x0
ctime = Thu Jan 01 08:00:00 CST 1970
mZxid = 0x0
mtime = Thu Jan 01 08:00:00 CST 1970
pZxid = 0x6
cversion = 2
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 0
numChildren = 2
```

### 监听器 get path watch

- 使用`get path watch`注册的监听器在`节点内容`发生改变时，向客户端发送通知，注意 Zookeeper 的触发器是一次性的，触发一次后会立即生效。

```bash
get /node1 watch

## 改变节点数据
set /node1 bbb

## 监听到节点内容改变了
WATCHER::
WatchedEvent state:SyncConnected type:NodeDataChanged path:/node1
```

### 监听器 stat path watch

- `stat path watch`注册的监听器能够在`节点状态`发生改变时向客户端发出通知。比如节点数据改变、节点被删除等。

```bash
stat /node2 watch

## 删除节点node2
delete /node2

## 监听器监听到了节点删除
WATCHER::
WatchedEvent state:SyncConnected type:NodeDeleted path:/node2
```

### 监听器 ls/ls2 path watch

- 使用`ls path watch`或者`ls2 path watch`注册的监听器，能够监听到该节点下的子节点的`增加`和`删除`操作。

```bash
ls /node1 watch

## 创建子节点
create /node1/b b

## 监听到了子节点的新增
WATCHER::
WatchedEvent state:SyncConnected type:NodeChildrenChanged path:/node1
```

## Zookeeper 的 ACL 权限控制

- zookeeper 类似文件控制系统，client 可以创建，删除，修改，查看节点，那么如何做到权限控制的呢？zookeeper 的`access control list` 访问控制列表可以做到这一点。
- ACL 权限控制，使用`scheme:id:permission`来标识。
  - `权限模式(scheme)`：授权的策略
  - `授权对象(id)`：授权的对象
  - `权限(permission)`：授予的权限
- 权限控制是基于每个节点的，需要对每个节点设置权限。
- 每个节点支持设置多种权限控制方案和多个权限。
- 子节点不会继承父节点的权限，客户端无权访问某节点，但可能可以访问它的子节点。
- 例如：根据 IP 地址进行授权，命令如下：

```bash
setACl /node1 ip:192.168.10.1:crdwa
```

### 权限模式

- 权限模式即是采用何种方式授权。
- `world`：只有一个用户，anyone，表示登录 zookeeper 所有人（默认的模式）。
- `ip`：对客户端使用 IP 地址认证。
- `auth`：使用已添加认证的用户认证。
- `digest`：使用`用户名:密码`方式认证。

### 授权对象

- 给谁授权，授权对象的 ID 指的是权限赋予的实体，例如 IP 地址或用户。

### 授予的权限

- 授予的权限包括`create`、`delete`、`read`、`writer`、`admin`。也就是增、删、改、查、管理的权限，简写`cdrwa`。
- **注意**：以上 5 种权限中，`delete`是指对子节点的删除权限，其他 4 种权限是对自身节点的操作权限。
- `create`：简写`c`，可以创建子节点。
- `delete`：简写`d`，可以删除子节点（仅下一级节点）。
- `read`：简写`r`，可以读取节点数据以及显示子节点列表。
- `write`：简写`w`，可以更改节点数据。
- `admin`：简写`a`，可以设置节点访问控制列表权限。

### 授权相关命令

- `getAcl [path]`：读取指定节点的 ACL 权限。
- `setAcl [path] [acl]`：设置 ACL
- `addauth <scheme> <auth>`：添加认证用户，和 auth，digest 授权模式相关。

### world 授权模式案例

- zookeeper 中默认的授权模式，针对登录 zookeeper 的任何用户授予指定的权限。命令如下：

```bash
setAcl [path] world:anyone:[permission]
```

- `path`：节点
- `permission`：授予的权限，比如`cdrwa`
- 去掉不能读取节点数据的权限：

```bash
## 获取权限列表（默认的）
getAcl /node2

'world,'anyone
: cdrwa

## 去掉读取节点数据的的权限，去掉r
setAcl /node2 world:anyone:cdwa

## 再次获取权限列表
getAcl /node2

'world,'anyone
: cdwa

## 获取节点数据，没有权限，失败
get /node2

Authentication is not valid : /node2
```

### IP 授权模式案例

- 针对登录用户的 ip 进行限制权限。命令如下：

```bash
setAcl [path] ip:[ip]:[acl]
```

- 远程登录 zookeeper 的命令如下：

```bash
./zkCli.sh -server ip
```

- 设置`192.168.10.1`这个 ip 的增删改查管理的权限。

```bash
setAcl /node2 ip:192.168.10.1:crdwa
```

### Auth 授权模式案例

- auth 授权模式需要有一个认证用户，添加命令如下：

```bash
addauth digest [username]:[password]
```

- 设置 auth 授权模式命令如下：

```bash
setAcl [path] auth:[user]:[acl]
```

- 为`chenmou`这个账户添加 cdrwa 权限：

```bash
## 添加一个认证账户
addauth digest chenmou:123456

## 添加权限
setAcl /node2 auth:chenmou:crdwa
```

### 多种模式授权

- zookeeper 中同一个节点可以使用多种授权模式，多种授权模式用`,`分隔。

```bash
## 创建节点
create /node3

## 添加认证用户
addauth chenmou:123456

## 添加多种授权模式
setAcl /node3 ip:192.178.10.1:crdwa,auth:chenmou:crdwa
```

### ACL 超级管理员

- zookeeper 的权限管理模式有一种叫做`super`，该模式提供一个超管可以方便的访问任何权限的节点。
- 假设这个超管是`super:admin`，需要先为超管生成密码的密文：

```bash
echo -n super:admin | openssl dgst  -binary -sha1 |openssl base64

## 执行完生成了秘钥
xQJmxLMiHGwaqBvst5y6rkB6HQs=
```

- 打开`zookeeper`目录下`/bin/zkServer.sh`，找到如下一行：

```bash
nohup JAVA&quot;−Dzookeeper.log.dir=JAVA"−Dzookeeper.log.dir={ZOO_LOG_DIR}" "-Dzookeeper.root.logger=${ZOO_LOG4J_PROP}"
```

- 在后面添加一行脚本，如下：

```bash
"-Dzookeeper.DigestAuthenticationProvider.superDigest=super:xQJmxLMiHGwaqBvst5y6rkB6HQs="
```

- 此时完整的脚本如下：

```bash
nohup "$JAVA" "-Dzookeeper.log.dir=${ZOO_LOG_DIR}" "-Dzookeeper.root.logger=${ZOO_LOG4J_PROP}" "-Dzookeeper.DigestAuthenticationProvider.superDigest=super:xQJmxLMiHGwaqBvst5y6rkB6HQs=" \
    -cp "$CLASSPATH" $JVMFLAGS $ZOOMAIN "$ZOOCFG" > "$_ZOO_DAEMON_OUT" 2>&1 < /dev/null &
```

- 重启 zookeeper
- 重启完成之后此时超管即配置完成，如果需要使用，则使用如下命令：

```bash
 addauth digest super:admin
```

## Curator 客户端

- Curator 是 Netflix 公司开源的一个 Zookeeper 客户端，与 Zookeeper 提供的原生客户端相比，Curator 的抽象层次更高，简化了 Zookeeper 客户端的开发量。

### 添加依赖

```xml
	<dependency>
            <groupId>org.apache.curator</groupId>
            <artifactId>curator-recipes</artifactId>
            <version>4.0.0</version>
            <exclusions>
                <exclusion>
                    <groupId>org.apache.zookeeper</groupId>
                    <artifactId>zookeeper</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

        <dependency>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
            <version>3.4.10</version>
        </dependency>
```

### 建立连接

- 客户端建立与 Zookeeper 的连接，这里仅仅演示单机版本的连接，如下：

```java
//创建CuratorFramework，用来操作api
CuratorFramework  client = CuratorFrameworkFactory.builder()
    //ip地址+端口号，如果是集群，逗号分隔
    .connectString("120.26.101.207:2181")
    //会话超时时间
    .sessionTimeoutMs(5000)
    //超时重试策略,RetryOneTime：超时重连仅仅一次
    .retryPolicy(new RetryOneTime(3000))
    //命名空间，父节点，如果不指定是在根节点下
    .namespace("node4")
    .build();
//启动
client.start();
```

### 重连策略

- 会话连接策略，即是当客户端与 Zookeeper 断开连接之后，客户端重新连接 Zookeeper 时使用的策略，比如重新连接一次。
- `RetryOneTime：`N 秒后重连一次，仅仅一次，演示如下：

```java
.retryPolicy(new RetryOneTime(3000))
```

- `RetryNTimes`：每 n 秒重连一次，重连 m 次。演示如下：

```java
//每三秒重连一次，重连3次。arg1：多长时间后重连，单位毫秒，arg2：总共重连几次
.retryPolicy(new RetryNTimes(3000,3))
```

- `RetryUntilElapsed`：设置了最大等待时间，如果超过这个最大等待时间将会不再连接。

```java
//每三秒重连一次，等待时间超过10秒不再重连。arg1：总等待时间，arg2：多长时间重连，单位毫秒
.retryPolicy(new RetryUntilElapsed(10000,3000))
```

### 新增节点

- 新增节点

```java
client.create()
    //指定节点的类型。PERSISTENT：持久化节点，PERSISTENT_SEQUENTIAL：持久化有序节点，EPHEMERAL：临时节点，EPHEMERAL_SEQUENTIAL临时有序节点
    .withMode(CreateMode.PERSISTENT)
    //指定权限列表，OPEN_ACL_UNSAFE：world:anyone:crdwa
    .withACL(ZooDefs.Ids.OPEN_ACL_UNSAFE)
    //写入节点数据，arg1:节点名称 arg2:节点数据
    .forPath("/a", "a".getBytes());
```

- 自定义权限列表：`withACL(acls)`方法中可以设置自定义的权限列表，代码如下：

```java
//自定义权限列表
List<ACL> acls=new ArrayList<>();
//指定授权模式和授权对象 arg1:授权模式，arg2授权对象
Id id=new Id("ip","127.0.0.1");
//指定授予的权限，ZooDefs.Perms.ALL:crdwa
acls.add(new ACL(ZooDefs.Perms.ALL,id));
client.create()
    .withMode(CreateMode.PERSISTENT)
    //指定自定义权限列表
    .withACL(acls)
    .forPath("/b", "b".getBytes());
```

- 递归创建节点：`creatingParentsIfNeeded()`方法对于创建多层节点，如果其中一个节点不存在的话会自动创建

```java
//递归创建节点
client.create()
    //递归方法，如果节点不存在，那么创建该节点
    .creatingParentsIfNeeded()
    .withMode(CreateMode.PERSISTENT)
    .withACL(ZooDefs.Ids.OPEN_ACL_UNSAFE)
    //test节点和b节点不存在，递归创建出来
    .forPath("/test/a", "a".getBytes());
```

- 异步创建节点：`inBackground()`方法可以异步回调创建节点，创建完成后会自动回调实现的方法

```java
 //异步创建节点
client.create()
    .withMode(CreateMode.PERSISTENT)
    .withACL(ZooDefs.Ids.OPEN_ACL_UNSAFE)
    //异步创建
    .inBackground(new BackgroundCallback() {
        /**
        * @param curatorFramework 客户端对象
        * @param curatorEvent 事件对象
        */
        @Override
        public void processResult(CuratorFramework curatorFramework, CuratorEvent curatorEvent) throws Exception {
            //打印事件类型
            System.out.println(curatorEvent.getType());
            }
    })
    .forPath("/test1", "a".getBytes());
```

### 更新节点数据

- 更新节点，当节点不存在会报错，代码如下：

```java
client.setData()
      .forPath("/a","a".getBytes());
```

- 携带版本号更新节点，当版本错误拒绝更新

```java
client.setData()
    //指定版本号更新，如果版本号错误则拒绝更新
    .withVersion(1)
    .forPath("/a","a".getBytes());
```

- 异步更新节点数据：

```java
client.setData()
        //异步更新
        .inBackground(new BackgroundCallback() {
        //回调方法
        @Override
        public void processResult(CuratorFramework curatorFramework, CuratorEvent curatorEvent) throws Exception {
                   }
        })
		.forPath("/a","a".getBytes());
```

### 删除节点

- 删除当前节点，如果有子节点则拒绝删除

```java
client.delete()
    //删除节点，如果是该节点包含子节点，那么不能删除
    .forPath("/a");
```

- 指定版本号删除，如果版本错误则拒绝删除

```java
client.delete()
    //指定版本号删除
    .withVersion(1)
    //删除节点，如果是该节点包含子节点，那么不能删除
    .forPath("/a");
```

- 如果当前节点包含子节点则一并删除，使用`deletingChildrenIfNeeded()`方法

```java
client.delete()
    //如果删除的节点包含子节点则一起删除
    .deletingChildrenIfNeeded()
    //删除节点，如果是该节点包含子节点，那么不能删除
    .forPath("/a");
```

- 异步删除节点，使用`inBackground()`

```java
client.delete()
	.deletingChildrenIfNeeded()
	//异步删除节点
    .inBackground(new BackgroundCallback() {
        @Override
        public void processResult(CuratorFramework curatorFramework, CuratorEvent curatorEvent) throws Exception {
        //回调监听
        }
       })
    //删除节点，如果是该节点包含子节点，那么不能删除
    .forPath("/a");
```

### 获取节点数据

- 同步获取节点数据

```java
byte[] bytes = client.getData().forPath("/node1");
System.out.println(new String(bytes));
```

- 获取节点状态和数据

```java
//保存节点状态
Stat stat=new Stat();
byte[] bytes = client.getData()
	//获取节点状态存储在stat对象中
    .storingStatIn(stat)
    .forPath("/node1");
System.out.println(new String(bytes));
//获取节点数据的长度
System.out.println(stat.getDataLength());
```

- 异步获取节点数据

```java
client.getData()
    //异步获取节点数据，回调监听
     .inBackground((curatorFramework, curatorEvent) -> {
          //节点数据
          System.out.println(new String(curatorEvent.getData()));
      })
     .forPath("/node1");
```

### 获取子节点

- 同步获取全部子节点

```java
 List<String> strs = client.getChildren().forPath("/");
        for (String str:strs) {
            System.out.println(str);
        }
```

- 异步获取全部子节点

```java
client.getChildren()
//异步获取
.inBackground((curatorFramework, curatorEvent) -> {
        List<String> strs = curatorEvent.getChildren();
        for (String str:strs) {
              System.out.println(str);
        }
  })
.forPath("/");
```

### 查看节点是否存在

- 同步查看

```java
//如果节点不存在，stat为null
Stat stat = client.checkExists().forPath("/node");
```

- 异步查看

```java
//如果节点不存在，stat为null
client.checkExists()
    .inBackground((curatorFramework, curatorEvent) -> {
    //如果为null则不存在
    System.out.println(curatorEvent.getStat());
    })
    .forPath("/node");
```

### Watcher API

- curator 提供了两种 watcher 来监听节点的变化
  - `NodeCache`：监听一个特定的节点，监听新增和修改
  - `PathChildrenCache`：监听一个节点的子节点，当一个子节点增加、删除、更新时，path Cache 会改变他的状态，会包含最新的子节点的数据和状态。
- NodeCache 演示：

```java
//arg1:连接对象 arg2:监听的节点路径,/namespace/path
final NodeCache nodeCache = new NodeCache(client, "/w1");
//启动监听
nodeCache.start();
//添加监听器
nodeCache.getListenable().addListener(() -> {
    //节点路径
    System.out.println(nodeCache.getCurrentData().getPath());
    //节点数据
    System.out.println(new String(nodeCache.getCurrentData().getData()));
});
//睡眠100秒
Thread.sleep(1000000);
//关闭监听
nodeCache.close();
```

- `PathChildrenCache`演示：

```java
//arg1：连接对象 arg2：节点路径  arg3:是否能够获取节点数据
PathChildrenCache cache=new PathChildrenCache(client,"/w1", true);
cache.start();
cache.getListenable().addListener((curatorFramework, pathChildrenCacheEvent) -> {
	//节点路径
	System.out.println(pathChildrenCacheEvent.getData().getPath());
	//节点状态
	System.out.println(pathChildrenCacheEvent.getData().getStat());
	//节点数据
	System.out.println(new String(pathChildrenCacheEvent.getData().getData()));
});
cache.close();
```

## 小福利

- 是不是觉得文章太长看得头晕脑胀，为此陈某特地将本篇文章制作成 PDF 文本，需要回去仔细研究的朋友，老规矩，回复关键词`ZK入门指南`。

