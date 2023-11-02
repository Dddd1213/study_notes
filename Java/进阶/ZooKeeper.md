# ZooKeeper

[TOC]

# 1

## 安装

- 位置：在/opt目录下
- 安装：黑马dubbo课程第7节
- 先进入bin目录下
- 启动：./zkServer.sh start
- 停止：./zkServer.sh stop
- 查看状态：./zkServer.sh status
- 默认端口：2181



## 简介

- apache Hadoop下的一个子项目，树形目录服务
- 节点可以存数据和节点信息
- 分布式，开源的，分布式应用程序的协调服务
  - 配置管理
  - 分布式锁
  - 集群管理（作为注册中心）



# 2

# ZooKeeper命令操作

## 数据模型

- 树形
- 节点分类
  - 持久化节点
  - 临时节点 -e
  - 持久化顺序节点 -s
  - 临时顺序节点 -es

## 服务端命令

- 启动 : ./zkServer.sh start
- 停止：./zkServer.sh stop
- 查看状态：./zkServer.sh status
- 重启：./zkServer.sh restart

## 客户端命令

- 连接：./zkCli.sh -server localhost:2181
- 退出：quit
-  查看节点：ls /
- 创建：create 不能重复
- 获取：get
- 设置数据：set
- 删除：delete
  - 如果节点不为空：deleteall

![image-20231031214923476](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231031214923476.png)

- 创建临时节点：加-e
  - 当前会话关掉（quit）就会消失
- 顺序：加 -s

![image-20231031215156816](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231031215156816.png)

- 临时顺序：-es
- 查看详细信息：ls -s



# 3

# JavaAPI操作

## Curator

- 记得和ZooKeeper的版本对应（可以去官网搜）


### 操作

- 建立连接

```java
 @Test
    public void testCurator(){
        /**
         * Create a new client
         * Params:
         * connectString – list of servers to connect to
         * sessionTimeoutMs – session timeout
         * connectionTimeoutMs – connection timeout
         * retryPolicy – retry policy to use
         * Returns:
         * client
         */
        ExponentialBackoffRetry retry = new ExponentialBackoffRetry(3000, 10);
//        CuratorFramework client = CuratorFrameworkFactory.newClient("192.8.2.5:2181", retry);
//        client.start();

        /*
        * 方法二
        *
        * */
         CuratorFramework client = CuratorFrameworkFactory.builder()
                .connectString("192.8.2.5:2181")
                .namespace("xiaoyangii")//相当于默认以xiaoyangii为结点根目录
                .retryPolicy(retry).build();
        client.start();
    }
```

- 添加

### 看不见源码？在cmd里执行：

- mvn dependency:resolve -Dclassifier=sources

- 然后 download sources

### 创建

- @Before 在任何test方法执行前执行
- 若创建节点没有指定数据，那么默认为当前ip

```java
client.create().forPath("/app1","haah".getBytes());
```

- 创建多级节点

  ```java
  .creatingParentContainersIfNeeded()
  ```

### 查询

```java
//查询数据
client.getData().forPath("/app1");
//查询子节点
client.getChildren.forPath("/");
//查询节点状态信息
Stat stat = new Stat();
client.getData().storingStaIn(stat).forPath("/app1")
```

### 修改

```java
client.setData().forPath("/app1","xixi".getBytes())
    //确认版本信息，防止多线程冲突
 Stat stat = new Stat();
client.getData().storingStaIn(stat).forPath("/app1")
int version = stat.getVersion()
client.setData().withVersion(version).forPath("/app1","xixi".getBytes()) 
```

### 删除

```java
//删除单个
client.delete().forPath("/app1");
//删除带子节点的节点
client.delete().deletingChildrenIfNeeded().forPath("/app1");
//必须成功的删除(机器抖动)
client.guaranted().forPath("/app1");
//回调
client.delete().deletingChildrenIfNeeded().inBackground(new BackgroundCallback() {
    @Override
    public void processResult(CuratorFramework client, CuratorEvent event) throws Exception {
        System.out.println("shanchu");
    }
}).forPath("/app1");
```

### 事件监听

- 三种Watcher
  - NodeCache：监控特定节点
  - PathChildrenCache：监控它的子节点
  - TreeCache：监控它和它的子节点

```java
 public void testNodeCache() throws Exception {
        NodeCache nodeCache = new NodeCache(client, "/app1");
        nodeCache.getListenable().addListener(new NodeCacheListener() {
            @Override
            public void nodeChanged() throws Exception {
                System.out.println("结点变化");
                //获取修改后的数据
                byte[] data = nodeCache.getCurrentData().getData();
                System.out.println(new String(data));
            }
        });
        //开启监听，如果设为true，则开启监听时加载缓冲数据
        nodeCache.start(true);
        while (true){
        }
    }
```

```java
/**
 * 演示 PathChildrenCache：监听某个节点的所有子节点们
 */
@Test
public void testPathChildrenCache() throws Exception {
    //1.创建监听对象
    PathChildrenCache pathChildrenCache = new PathChildrenCache(client,"/app2",true);

    //2. 绑定监听器
    pathChildrenCache.getListenable().addListener(new PathChildrenCacheListener() {
        @Override
        public void childEvent(CuratorFramework client, PathChildrenCacheEvent event) throws Exception {
            System.out.println("子节点变化了~");
            System.out.println(event);
            //监听子节点的数据变更，并且拿到变更后的数据
            //1.获取类型
            PathChildrenCacheEvent.Type type = event.getType();
            //2.判断类型是否是update
            if(type.equals(PathChildrenCacheEvent.Type.CHILD_UPDATED)){
                System.out.println("数据变了！！！");
                byte[] data = event.getData().getData();
                System.out.println(new String(data));

            }
        }
    });
    //3. 开启
    pathChildrenCache.start();

    while (true){

    }
}
```

```java
/**
 * 演示 TreeCache：监听某个节点自己和所有子节点们
 */

@Test
public void testTreeCache() throws Exception {
    //1. 创建监听器
    TreeCache treeCache = new TreeCache(client,"/app2");

    //2. 注册监听
    treeCache.getListenable().addListener(new TreeCacheListener() {
        @Override
        public void childEvent(CuratorFramework client, TreeCacheEvent event) throws Exception {
            System.out.println("节点变化了");
            System.out.println(event);
        }
    });

    //3. 开启
    treeCache.start();

    while (true){

    }
}
```



## 分布式锁

- 处理跨机器的进程之间的数据同步问题
  - 缓存：Redis（不太可靠，如果master节点挂了）
  - ZooKeeper
  - 数据库
- 原理：
  - 获取锁创建**临时顺序**节点
  - 如果自己创建的子节点序号最小，就获取到锁
  - 如果不是最小，则需要找到比自己小的一个节点，然后监听删除事件
  - 使用完删除节点

### 模拟12306

- 资源在哪，锁就在哪
- 开始前加锁，结束后放锁
- 在构造方法里创建锁

```java
public class Ticket12306 implements Runnable{

    private int tickets = 10;//数据库的票数

    private InterProcessMutex lock ;

    public Ticket12306(){
        //重试策略
        RetryPolicy retryPolicy = new ExponentialBackoffRetry(3000, 10);
        //2.第二种方式
        //CuratorFrameworkFactory.builder();
        CuratorFramework client = CuratorFrameworkFactory.builder()
                .connectString("192.168.149.135:2181")
                .sessionTimeoutMs(60 * 1000)
                .connectionTimeoutMs(15 * 1000)
                .retryPolicy(retryPolicy)
                .build();

        //开启连接
        client.start();

        lock = new InterProcessMutex(client,"/lock");
    }

    @Override
    public void run() {

        while(true){
            //获取锁
            try {
                lock.acquire(3, TimeUnit.SECONDS);
                if(tickets > 0){

                    System.out.println(Thread.currentThread()+":"+tickets);
                    Thread.sleep(100);
                    tickets--;
                }
            } catch (Exception e) {
                e.printStackTrace();
            }finally {
                //释放锁
                try {
                    lock.release();
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```



# 4

# 集群

## 集群选举

- serviceId，编号越大权重越大
- 先超过半数，就是leader

## 集群创建

- 看同目录下另一个md
