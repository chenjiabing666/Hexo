---
title: Zookeeper实现分布式锁
date: 2020-04-19 15:48:54
categories: 杂谈
tags: 杂谈
---
## 导读

- 真是有人(`锁`)的地方就有江湖(`事务`)，今天不谈江湖，来撩撩人。
- 分布式锁的概念、为什么使用分布式锁，想必大家已经很清楚了。前段时间作者写过Redis是如何实现分布式锁，今天这篇文章来谈谈Zookeeper是如何实现分布式锁的。
- 陈某今天分别从如下几个方面来详细讲讲ZK如何实现分布式锁：
  1. **ZK的四种节点**
  2. **排它锁的实现**
  3. **读写锁的实现**
  4. **Curator实现分步式锁**


## ZK的四种节点

- 持久性节点：节点创建后将会一直存在
- 临时节点：临时节点的生命周期和当前会话绑定，一旦当前会话断开临时节点也会删除，当然可以主动删除。
- 持久有序节点：节点创建一直存在，并且zk会自动为节点加上一个自增的后缀作为新的节点名称。
- 临时有序节点：保留临时节点的特性，并且zk会自动为节点加上一个自增的后缀作为新的节点名称。



## 排它锁的实现

- 排他锁的实现相对简单一点，利用了**zk的创建节点不能重名的特性**。如下图：

![](https://gitee.com/chenjiabing666/Blog-file/raw/master/ZK%E5%88%86%E5%B8%83%E5%BC%8F%E9%94%81/zk%E6%8E%92%E4%BB%96%E9%94%81.png)

- 根据上图分析大致分为如下步骤：
  1. 尝试获取锁：创建`临时节点`，zk会保证只有一个客户端创建成功。
  2. 创建临时节点成功，获取锁成功，执行业务逻辑，业务执行完成后删除锁。
  3. 创建临时节点失败，阻塞等待。
  4. 监听删除事件，一旦临时节点删除了，表示互斥操作完成了，可以再次尝试获取锁。
  5. 递归：获取锁的过程是一个递归的操作，`获取锁->监听->获取锁`。
- **如何避免死锁**：创建的是临时节点，当服务宕机会话关闭后临时节点将会被删除，锁自动释放。

### 代码实现

- 作者参照JDK锁的实现方式加上模板方法模式的封装，封装接口如下：

```java
/**
 * @Description ZK分布式锁的接口
 * @Author 陈某
 * @Date 2020/4/7 22:52
 */
public interface ZKLock {
    /**
     * 获取锁
     */
    void lock() throws Exception;

    /**
     * 解锁
     */
    void unlock() throws Exception;
}
```

- 模板抽象类如下：

```java
/**
 * @Description 排他锁，模板类
 * @Author 陈某
 * @Date 2020/4/7 22:55
 */
public abstract class AbstractZKLockMutex implements ZKLock {

    /**
     * 节点路径
     */
    protected String lockPath;

    /**
     * zk客户端
     */
    protected CuratorFramework zkClient;

    private AbstractZKLockMutex(){}

    public AbstractZKLockMutex(String lockPath,CuratorFramework client){
        this.lockPath=lockPath;
        this.zkClient=client;
    }

    /**
     * 模板方法，搭建的获取锁的框架，具体逻辑交于子类实现
     * @throws Exception
     */
    @Override
    public final void lock() throws Exception {
        //获取锁成功
        if (tryLock()){
            System.out.println(Thread.currentThread().getName()+"获取锁成功");
        }else{  //获取锁失败
            //阻塞一直等待
            waitLock();
            //递归，再次获取锁
            lock();
        }
    }

    /**
     * 尝试获取锁，子类实现
     */
    protected abstract boolean tryLock() ;


    /**
     * 等待获取锁，子类实现
     */
    protected abstract void waitLock() throws Exception;


    /**
     * 解锁：删除节点或者直接断开连接
     */
    @Override
    public  abstract void unlock() throws Exception;
}
```

- 排他锁的具体实现类如下：

```java
/**
 * @Description 排他锁的实现类，继承模板类 AbstractZKLockMutex
 * @Author 陈某
 * @Date 2020/4/7 23:23
 */
@Data
public class ZKLockMutex extends AbstractZKLockMutex {

    /**
     * 用于实现线程阻塞
     */
    private CountDownLatch countDownLatch;

    public ZKLockMutex(String lockPath,CuratorFramework zkClient){
        super(lockPath,zkClient);
    }

    /**
     * 尝试获取锁：直接创建一个临时节点，如果这个节点存在创建失败抛出异常，表示已经互斥了，
     * 反之创建成功
     * @throws Exception
     */
    @Override
    protected boolean tryLock()  {
        try {
            zkClient.create()
                    //临时节点
                    .withMode(CreateMode.EPHEMERAL)
                    //权限列表 world:anyone:crdwa
                    .withACL(ZooDefs.Ids.OPEN_ACL_UNSAFE)
                    .forPath(lockPath,"lock".getBytes());
            return true;
        }catch (Exception ex){
            return false;
        }
    }


    /**
     * 等待锁，一直阻塞监听
     * @return  成功获取锁返回true，反之返回false
     */
    @Override
    protected void waitLock() throws Exception {
        //监听节点的新增、更新、删除
        final NodeCache nodeCache = new NodeCache(zkClient, lockPath);
        //启动监听
        nodeCache.start();
        ListenerContainer<NodeCacheListener> listenable = nodeCache.getListenable();

        //监听器
        NodeCacheListener listener=()-> {
            //节点被删除，此时获取锁
            if (nodeCache.getCurrentData() == null) {
                //countDownLatch不为null，表示节点存在，此时监听到节点删除了，因此-1
                if (countDownLatch != null)
                    countDownLatch.countDown();
            }
        };
        //添加监听器
        listenable.addListener(listener);

        //判断节点是否存在
        Stat stat = zkClient.checkExists().forPath(lockPath);
        //节点存在
        if (stat!=null){
            countDownLatch=new CountDownLatch(1);
            //阻塞主线程，监听
            countDownLatch.await();
        }
        //移除监听器
        listenable.removeListener(listener);
    }

    /**
     * 解锁，直接删除节点
     * @throws Exception
     */
    @Override
    public void unlock() throws Exception {
        zkClient.delete().forPath(lockPath);
    }
}
```



### 可重入性排他锁如何设计

- 可重入的逻辑很简单，在本地保存一个`ConcurrentMap`，`key`是当前线程，`value`是定义的数据，结构如下：

```java
 private final ConcurrentMap<Thread, LockData> threadData = Maps.newConcurrentMap();
```

- 重入的伪代码如下：

```java
public boolean tryLock(){
    //判断当前线程是否在threadData保存过
    //存在，直接return true
    //不存在执行获取锁的逻辑
    //获取成功保存在threadData中
}
```



## 读写锁的实现

- 读写锁分为读锁和写锁，区别如下：
  - 读锁允许多个线程同时读数据，但是在读的同时不允许写线程修改。
  - 写锁在获取后，不允许多个线程同时写或者读。
- 如何实现读写锁？ZK中有一类节点叫临时有序节点，上文有介绍。下面我们来利用临时有序节点来实现读写锁的功能。



### 读锁的设计

- 读锁允许多个线程同时进行读，并且在读的同时不允许线程进行写操作，实现原理如下图：

![](https://gitee.com/chenjiabing666/Blog-file/raw/master/ZK%E5%88%86%E5%B8%83%E5%BC%8F%E9%94%81/%E8%AF%BB%E9%94%81.png)

- 根据上图，获取一个读锁分为以下步骤：
  1. 创建临时有序节点（当前线程拥有的`读锁`或称作`读节点`）。
  2. 获取路径下所有的子节点，并进行`从小到大`排序
  3. 获取当前节点前的临近写节点(写锁)。
  4. 如果不存在的临近写节点，则成功获取读锁。
  5. 如果存在临近写节点，对其监听删除事件。
  6. 一旦监听到删除事件，**重复2,3,4,5的步骤(递归)**。



### 写锁的设计

- 线程一旦获取了写锁，不允许其他线程读和写。实现原理如下：

![](https://gitee.com/chenjiabing666/Blog-file/raw/master/ZK%E5%88%86%E5%B8%83%E5%BC%8F%E9%94%81/%E5%86%99%E9%94%81.png)



- 从上图可以看出唯一和写锁不同的就是监听的节点，这里是监听临近节点(读节点或者写节点)，读锁只需要监听写节点，步骤如下：
  1. 创建临时有序节点（当前线程拥有的`写锁`或称作`写节点`）。
  2. 获取路径下的所有子节点，并进行`从小到大`排序。
  3. 获取当前节点的临近节点(读节点和写节点)。
  4. 如果不存在临近节点，则成功获取锁。
  5. 如果存在临近节点，对其进行监听删除事件。
  6. 一旦监听到删除事件，**重复2,3,4,5的步骤(递归)**。



### 如何监听

- 无论是写锁还是读锁都需要监听前面的节点，不同的是读锁只监听临近的写节点，写锁是监听临近的所有节点，抽象出来看其实是一种链式的监听，如下图：

![](https://gitee.com/chenjiabing666/Blog-file/raw/master/ZK%E5%88%86%E5%B8%83%E5%BC%8F%E9%94%81/%E9%93%BE%E5%BC%8F.png)

- 每一个节点都在监听前面的临近节点，一旦前面一个节点删除了，再从新排序后监听前面的节点，这样递归下去。



### 代码实现

- 作者简单的写了读写锁的实现，先造出来再优化，不建议用在生产环境。代码如下：

```java
public class ZKLockRW  {

    /**
     * 节点路径
     */
    protected String lockPath;

    /**
     * zk客户端
     */
    protected CuratorFramework zkClient;

    /**
     * 用于阻塞线程
     */
    private CountDownLatch countDownLatch=new CountDownLatch(1);


    private final static String WRITE_NAME="_W_LOCK";

    private final static String READ_NAME="_R_LOCK";


    public ZKLockRW(String lockPath, CuratorFramework client) {
        this.lockPath=lockPath;
        this.zkClient=client;
    }

    /**
     * 获取锁，如果获取失败一直阻塞
     * @throws Exception
     */
    public void lock() throws Exception {
        //创建节点
        String node = createNode();
        //阻塞等待获取锁
        tryLock(node);
        countDownLatch.await();
    }

    /**
     * 创建临时有序节点
     * @return
     * @throws Exception
     */
    private String createNode() throws Exception {
        //创建临时有序节点
       return zkClient.create()
                .withMode(CreateMode.EPHEMERAL_SEQUENTIAL)
                .withACL(ZooDefs.Ids.OPEN_ACL_UNSAFE)
                .forPath(lockPath);
    }

    /**
     * 获取写锁
     * @return
     */
    public  ZKLockRW writeLock(){
        return new ZKLockRW(lockPath+WRITE_NAME,zkClient);
    }

    /**
     * 获取读锁
     * @return
     */
    public  ZKLockRW readLock(){
        return new ZKLockRW(lockPath+READ_NAME,zkClient);
    }

    private void tryLock(String nodePath) throws Exception {
        //获取所有的子节点
        List<String> childPaths = zkClient.getChildren()
                .forPath("/")
                .stream().sorted().map(o->"/"+o).collect(Collectors.toList());


        //第一个节点就是当前的锁，直接获取锁。递归结束的条件
        if (nodePath.equals(childPaths.get(0))){
            countDownLatch.countDown();
            return;
        }

        //1. 读锁：监听最前面的写锁，写锁释放了，自然能够读了
        if (nodePath.contains(READ_NAME)){
            //查找临近的写锁
            String preNode = getNearWriteNode(childPaths, childPaths.indexOf(nodePath));
            if (preNode==null){
                countDownLatch.countDown();
                return;
            }
            NodeCache nodeCache=new NodeCache(zkClient,preNode);
            nodeCache.start();
            ListenerContainer<NodeCacheListener> listenable = nodeCache.getListenable();
            listenable.addListener(() -> {
                //节点删除事件
                if (nodeCache.getCurrentData()==null){
                    //继续监听前一个节点
                    String nearWriteNode = getNearWriteNode(childPaths, childPaths.indexOf(preNode));
                    if (nearWriteNode==null){
                        countDownLatch.countDown();
                        return;
                    }
                    tryLock(nearWriteNode);
                }
            });
        }

        //如果是写锁，前面无论是什么锁都不能读，直接循环监听上一个节点即可，直到前面无锁
        if (nodePath.contains(WRITE_NAME)){
            String preNode = childPaths.get(childPaths.indexOf(nodePath) - 1);
            NodeCache nodeCache=new NodeCache(zkClient,preNode);
            nodeCache.start();
            ListenerContainer<NodeCacheListener> listenable = nodeCache.getListenable();
            listenable.addListener(() -> {
                //节点删除事件
                if (nodeCache.getCurrentData()==null){
                    //继续监听前一个节点
                    tryLock(childPaths.get(childPaths.indexOf(preNode) - 1<0?0:childPaths.indexOf(preNode) - 1));
                }
            });
        }
    }

    /**
     * 查找临近的写节点
     * @param childPath 全部的子节点
     * @param index 右边界
     * @return
     */
    private String  getNearWriteNode(List<String> childPath,Integer index){
        for (int i = 0; i < index; i++) {
            String node = childPath.get(i);
            if (node.contains(WRITE_NAME))
                return node;

        }
        return null;
    }

}
```


## Curator实现分步式锁

- Curator是Netflix公司开源的一个Zookeeper客户端，与Zookeeper提供的原生客户端相比，Curator的抽象层次更高，简化了Zookeeper客户端的开发量。 
- Curator在分布式锁方面已经为我们封装好了，大致实现的思路就是按照作者上述的思路实现的。中小型互联网公司还是建议直接使用框架封装好的，毕竟稳定，有些大型的互联公司都是手写的，牛逼啊。
- 创建一个排他锁很简单，如下：

```java
//arg1：CuratorFramework连接对象，arg2：节点路径
lock=new InterProcessMutex(client,path);
//获取锁
lock.acquire();
//释放锁
lock.release();
```

- 更多的API请参照官方文档，不是此篇文章重点。

- **至此ZK实现分布式锁就介绍完了，如有想要源码的朋友，老规矩，回复关键词`分布式锁`获取。**

## 一点小福利
- 对于Zookeeper不太熟悉的朋友，陈某特地花费两天时间总结了ZK的常用知识点，包括ZK常用shell命令、ZK权限控制、Curator的基本操作API。目录如下：
![](https://gitee.com/chenjiabing666/Blog-file/raw/master/ZK%E5%88%86%E5%B8%83%E5%BC%8F%E9%94%81/5.png)
- **需要上面PDF文件的朋友，老规矩，回复关键词`ZK总结`。**








