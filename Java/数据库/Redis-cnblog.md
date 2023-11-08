# Redis

[TOC]

# 环境安装

## Linux

- 在虚拟机中安装
  - 参考的韩顺平第六节
- 问题：没有网络小图标也没网
- 解决：在虚拟网络编辑器里恢复默认设置然后重启解决
- xftp 7 向虚拟机传文件
- xshell 7 相当于远程使用虚拟机的终端



# 1

# 简介

- 前台启动：redis-server
- 后台启动（推荐）
  - cd /usr/local/bin
  - redis-server /etc/redis.conf
  - redis-cli 连接 
- 默认端口：6379
- 切换库：默认0,16个数据库
  - select <dbid\>
- 设置密码：.conf里requirepass
  - 连接完后 auth 密码，才可使用
- **单线程** + 多路IO复用，支持更多的数据类型，支持持久化

## key

- 查看所有key keys *
- 设置值 set <key\> <value\>
-  判断key是否存在 exists <key\>
- 查看键的类型  type <key\>
- 删除 del <key\>
- 非阻塞删除（异步删除） unlink <key\>
- 设置过期时间 expir <key\> <时间\>
- 查看还有多久过期 ttl <key\>
  - -1 永不过期
  - -2 已过期
- 查看key的数量 dbsize
- 清除当前库 flushdb
- 通杀当前库 flushall

# 2

# 常用数据类型

## String

- 取key对应的值 get <key\>
- 追加 append <key\><value\>
- 获取长度 strlen <key\>
- **设置值（只有key不存在的时候能用）setnx <key\><value\>**
  - 用作锁
- 数字增1 incr <key\>
- incrby <key\> <加减的值\>
- 减1 decr <key\>
- decrby <key\> <加减的值\>
- 一次设置多个 mset <key\><value\><key\><value\>
- 一次取多个 mget <key\> <key\> <key\>
- msetnx <key\><value\><key\><value\>（注意原子性）
- 获取范围值 getrange <key\> <开始位置\><结束位置\>
- 设置范围值 setrange <key\> <开始位置\><value\>
- 设置keyvalue的同时设置过期时间 setex <key\> <过期时间\><value\> 
- 设置新值并获得旧值 getset <key\> <value\>   



## List

- 单键多值
- 底层：将多个ziplist用双向指针串起来形成quicklist

- lpush/rpush <key\> <value1\> <value2\>... 加入 

- lrange  <key\> 起始 结束 （0 左边第一个 -1 右边第一个 ）

  ![image-20231021170709745](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231023222418959-737842781.png)

- lpop/rpop 从左/右吐一个值
  - 键在值在，值光键亡
- lpoprpush  <key1\> <key2\>
- lindex  <key\> <index\> （从左到右）按照索引下标获得元素
- llen  <key\> 获取列表长度
- linsert  <key\> before/after  "<value\>" "<newvalue\>" 在 <value\>前/后面插入 <newvalue\>
- lrem  <key\> <n\> <value\>从左删n个value
- lset  <key\> <index\> <value\> 设置值



## set

- 无序，自动去重
- 底层：hash表，添加等时间复杂度O（1）
- sadd 添加
- smenmbers  <key\> 取出所有值
- sismember  <key\>  <value\> 判断是否有value
- scard  <key\> 返回元素个数
- srem  <key\> <value2\> <value1\> 删除
- spop  <key\> **随机**吐一个
- srandmember <key\> <n\> 随机取n个（仍留在集合中）
- smove  <key1\> <key2\>  <value\> 把value从key1移动到key2
- sinter  <key1\> <key2\> 返回两个集合的交集元素
- sunion  <key1\> <key2\> 并集
- sdiff <key1\> <key2\> 差集（在1不在2）



## hash哈希

- key和field value，类似java中的map，适合存储对象

- hset <key\><field\><value\> 给<key\> 集合中的<field\>键赋值<value\>
- hget <key1\><field\>从<key1\> 集合<field\>取出value
- hmset <key1\>field1><value1\>field2><value2\>... 批量设置hash 的值
- hexists<key1\><field\> 查看哈希表key中，给定域field是否存在。
- hkeys<key\> 列出该hash集合的所有field
- hvals<key\>列出该hash集合的所有value 
- hincrby <key\><field\><increment\>为哈希表key中的域field 的值加上增量
- hsetnx <key\><field\><value\> 将哈希表key中的域field 的值设置为value，当且仅当域field不存在

## Zset

- 去重，有序
- hash+跳跃表，hash的field相当于value，value相当于score
- zadd <key\><score1\><value1\><score2\><value2\>...
- zrange <key\><key\><begin\><end\> (withscores)
- zrangebyscore <key\> [min max] 从小到大（默认）
- zrevrangebyscore <key\> [max min] 从大到小
- zincreby <key\> <num\><value\> 为元素的score增量num
- zrem <key\> <value\>删除
- zcount
- zrank <key\><value\> 返回排名

  

# 2

## 配置文件

- 路径：etc/redis.conf

## 发布和订阅

- subscribe aaa 订阅aaa

- publish  aaa hello 给aaa发布消息hello

  

# 3

# 新数据类型

## Bitmaps

- 看做一个只能存01的数组，索引下标叫“偏移量”
- 节约空间，存活跃量较大用户，记录每天是否访问

- setbit <key\> 偏移量 值
- getbit <key\> 偏移量
- bitcount <key\> 统计为1的数量，可加begin end（以byte为单位）
- bitop  交并与或非
  - bitop and key3 key1 key2：把key1和key2与的结果放到key3中 

## HyperLogLog

- 基数统计
- pfadd <key\> <element\>
- pfcount <key\> 统计基数
- pfmerge <key3\><key1\><key2\> 把12合并到3



补充：[Redis的Set类型、Sorted Set类型、Bitmap类型和HyperLogLog_redis set与hyperloglog_zmm0420的博客-CSDN博客](https://blog.csdn.net/zmm0420/article/details/126161803?ops_request_misc=%7B%22request%5Fid%22%3A%22169795337016800180636038%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=169795337016800180636038&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-3-126161803-null-null.142^v96^pc_search_result_base2&utm_term=HyperLogLog和set有什么区别&spm=1018.2226.3001.4187)

## Geo

- 地理位置，有效维度+-85度左右
- 已添加的数据不能再次添加
- geoadd <key\> 经度 纬度 城市名
- geopos <key\> 城市名
- geodist <key\> 城市1 城市2 m/km/ft/mi， 获取直线距离
- georadius <key\> 经度 纬度 半径  m/km/ft/mi，以给定经纬度为中心找出某一半径内的元素



# 4

# Jdedis

- Exception in thread "main" redis.clients.jedis.exceptions.JedisConnectionException: Failed connecting to host 192.168.232.129:6379
  - 防火墙问题：[redis连接超时--Exception in thread "main" redis.clients.jedis.exceptions.JedisConnectionException: Failed connecting to host xxxxx:6379 - 木子小僧 - 博客园 (cnblogs.com)](https://www.cnblogs.com/invban/p/14187524.html)

- 创建redis对象，可以用各种方法

```java
public void demo1(){
    Jedis jedis = new Jedis("192.168.232.129",6379);
    Set<String> keys = jedis.keys("*");
    for (String key: keys){
        System.out.println(key);
    }
}
```

## 验证码实战

- 获取6位随机验证码

```java
Random random = new Random();
String code = "";
for (int i=0;i<6;i++)
{
    int i1 = random.nextInt(10);
    code+=i1;
}
```

- 验证手机

  - 验证码两分钟过期
  - 一个手机**一天**只能验证三次
  - 思路：用两个key存，一个记录验证码和验证码过期时间（两分钟），一个记录用户访问了几次和用户过期时间（一天）
  - Tips：要保证key的唯一性，可以把电话号等信息编到key里

  ```java
  public static void verifyPhone(String phone,String code){
      Jedis jedis = new Jedis("192.168.232.129",6379);
      //俩key
      String countKey = "VerifyCode" + phone + ":count";
      String codeKey = "VerifyCode" + phone + ":code";
  
      String count = jedis.get("countKey");
      //没存过，存一个
      if(count==null)
      {
          jedis.setex("countKey",24*60*60,"1");
      }else if(Integer.parseInt(count)<3){
          jedis.incr(countKey);
      }
      else{
          System.out.println("今天已超过三次");
          return;
      }
  
      String vcode = getCode();
      jedis.setex("codeKey",120,vcode);
  
  }
  ```

- 验证验证码

```java
public static boolean verifyCode(String phone,String code){
    Jedis jedis = new Jedis("192.168.232.129",6379);
    String codeKey = "VerifyCode" + phone + ":code";

    String codekey1 = jedis.get("codeKey");
    if(codekey1==null){
        System.out.println("验证码已过期");
        return false;
    }
    if(codekey1.equals(code)){
        return true;
    }else{
        return false;
    }
}
```



## SpringBoot整合Redis

- 依赖

```xml
<!--redis启动器-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<!--redis依赖-->
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
</dependency>
<!--redis连接池依赖-->
<dependency>
    <groupId>org.apache.commons</groupId
    <artifactId>commons-pool2</artifactId>
</dependency>
```

- 配置连接信息

  - 单机

  ```yml
  spring:
    redis:
      host: 192.168.1.201
      port: 6379
      password: 123456
      timeout: 5000ms # 连接超时时间（毫秒）
      lettuce:
        pool:
          max-active: 20 # 连接池最大连接数（使用负值表示没有限制）
          max-idle: 10 # 连接池中的最大空闲连接
          min-idle: 5 # 连接池中的最小空闲连接
          max-wait: 5000ms # 连接池最大阻塞等待时间（使用负值表示没有限制）
  ```

  - 集群

```yml
spring:
  redis:
    host: 192.168.1.26
    port: 7001
    password: 123456
    timeout: 5000ms # 连接超时时间（毫秒）
    cluster:
      nodes: 192.168.1.26:7001,192.168.1.26:7002,192.168.1.26:7003,192.168.1.26:7004,192.168.1.26:7005,192.168.1.26:7006
    lettuce:
      pool:
        max-active: 20 # 连接池最大连接数（使用负值表示没有限制）
        max-idle: 10 # 连接池中的最大空闲连接
        min-idle: 5 # 连接池中的最小空闲连接
        max-wait: 5000ms # 连接池最大阻塞等待时间（使用负值表示没有限制）
```

- redis配置类

  - @EnableCaching：开启缓存
  - @Configuration

  ```java
  @EnableCaching
  @Configuration
  public class RedisConfig extends CachingConfigurerSupport {
   
      @Bean
      public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
          RedisTemplate<String, Object> template = new RedisTemplate<>();
          RedisSerializer<String> redisSerializer = new StringRedisSerializer();
          Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
          ObjectMapper om = new ObjectMapper();
          om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
          om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
          jackson2JsonRedisSerializer.setObjectMapper(om);
          template.setConnectionFactory(factory);
          //key序列化方式
          template.setKeySerializer(redisSerializer);
          //value序列化
          template.setValueSerializer(jackson2JsonRedisSerializer);
          //value hashmap序列化
          template.setHashValueSerializer(jackson2JsonRedisSerializer);
          return template;
      }
   
      @Bean
      public CacheManager cacheManager(RedisConnectionFactory factory) {
          RedisSerializer<String> redisSerializer = new StringRedisSerializer();
          Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
          //解决查询缓存转换异常的问题
          ObjectMapper om = new ObjectMapper();
          om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
          om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
          jackson2JsonRedisSerializer.setObjectMapper(om);
          // 配置序列化（解决乱码的问题）,过期时间600秒
          RedisCacheConfiguration config = RedisCacheConfiguration.defaultCacheConfig()
                  .entryTtl(Duration.ofSeconds(600))
                  .serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(redisSerializer))
                  .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(jackson2JsonRedisSerializer))
                  .disableCachingNullValues();
          RedisCacheManager cacheManager = RedisCacheManager.builder(factory)
                  .cacheDefaults(config)
                  .build();
          return cacheManager;
      }
  }
  ```

- redisTemplate调用redis相关操作



# 5

# 事务和锁机制

## 事务

- 串联多个命令防止插队

### 基本操作

- Multi：组队
- Exec：执行
- discard：放弃组队
- 若组队时有错误，则执行时全失败；若组队成功，执行有一个失败，那么仅该命令失败

## 事务冲突（悲观锁，乐观锁）

- 悲观锁：用之前锁，用完之后开锁
- 乐观锁：适用于**多读**；设版本号，每次修改先检查自己手中版本号和当前数据的版本号是否一致

![image-20231022171234793](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231023222418268-744556970.png)

- watch key1.... 写在multi之前，监视，若在事务执行前被监视的key被改动，事务被打断。



## 事务秒杀案例

- 记得每次return前都要关闭redis

  

### 高并发问题

- 下载ab工具（作用：模拟高并发)
- ab -n 请求数量 -c 并发数量 -p 提交参数 -T <contentType\> 请求地址
  - 提交参数需要建一个文件
  - contentType：enctype值

### 连接超时问题

- 连接池
- 先写一个JedisPoolUtil
- 然后把new代码改成从连接池获取对象

```java
//Jedis jedis = new Jedis("192.168.232.130", 6379);
JedisPool jedisPoolInstance = JedisPoolUtil.getJedisPoolInstance();
Jedis jedis = jedisPoolInstance.getResource();
```

### 超卖问题

- 使用事务

```java
    public static boolean doSecKill(String uid, String prodid) throws IOException{
        if(uid==null||prodid==null){
            return false;
        }

        Jedis jedis = new Jedis("192.168.232.130", 6379);

        String kcKey="sk:"+prodid+":qt";
        String usKey="sk:"+prodid+":user";

        //监视库存
        jedis.watch(kcKey);

        if(jedis.get(kcKey)==null)
        {
            System.out.println("秒杀未开始");
            jedis.close();
            return false;
        }
        if(jedis.sismember(usKey,uid))
        {
            System.out.println("该用户已参与过秒杀");
            jedis.close();
            return false;
        }
        if(Integer.parseInt(jedis.get(kcKey))<1)
        {
            System.out.println("秒杀已结束");
            jedis.close();
            return false;
        }

        //使用事务
        Transaction multi = jedis.multi();
        multi.decr(kcKey);
        multi.sadd(usKey,uid);
        List<Object> results = multi.exec();
        
        if(results==null||results.size()==0)
        {
            System.out.println("秒杀失败");
            jedis.close();
            return false;
        
//        jedis.decr(kcKey);
//        jedis.sadd(usKey,uid);
        System.out.println("秒杀成功");
        jedis.close();
        return true;
    }
```



### 库存遗留问题

- 由乐观锁造成，库存500，2000个人同时抢到，都获得1.0的版本号，但是只有一个人付款并把版本号改为1.1，1999人版本号不匹配抢不到，但库存还有499个
- 解决：LUA脚本语言（redis2.6以上）
- （没太听懂）

- 代码看这个：[SpringBoot使用LUA解决Redis库存遗留问题_Keeling1720的博客-CSDN博客](https://blog.csdn.net/weixin_44741023/article/details/121546227?ops_request_misc=%7B%22request%5Fid%22%3A%22169803325316800182775944%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=169803325316800182775944&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-121546227-null-null.142^v96^pc_search_result_base2&utm_term=LUA脚本解决库存遗留问题&spm=1018.2226.3001.4187)




# 6

# 持久化操作

## RBD

- 在指定时间间隔内将内存的**数据集快照**写入磁盘
  - 文件名：dump.rdb
- 子进程Fork：先写入一个临时文件，载替换上次那个持久化好的文件（保证数据一致性完整性blabla）
- 写时复制技术
- 默认开启
- 适合大规模数据恢复；对数据完整性一致性要求不高；节省磁盘空间；恢复速度快
- fork时有2倍膨胀空间；最后一次持久化后数据可能会丢失；

- 配置文件中改 save可以设置备份规则

  

## AOF

- 以日志的形式记录每一个写操作，只许追加文件不许改写文件

  - 文件名：appendonly.aof

- 默认不开启

  - redis.conf中appendonly改为yes

- 异常恢复：redis-check-aof --fix 文件名

- 配置 appendsync

  -  always 每次都会写入

  - everysec 每秒同步
  - no 不主动同步，同步时机交给操作系统

- Rewrite压缩：只关注最终结果；fork出一条子进程；文件大于64M且达到其两倍开始重写

  - eg.第一次为64M，重写后为50M，则第二次重写在100M时开始
  - eg.2 第一次为64M，重写后为20M，则第二次在64M时开始

- 临时文件



# 7

# 主从复制

- master写，slave读
  - 读写分离
  - 容灾快速恢复，一主多从

![image-20231023143957268](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231023222416996-1214722221.png)

## 搭建

- 创建/myredis文件夹
- 三个redis.conf配置文件，分三个端口
  - 公共配置用include引入
- info replication 查看主机运行情况
- 从机上：slaveof <ip\><port\> 

## 一主二仆

- 从服务器挂了再开会变成主，再设为从后，会把主服务器中的数据再从头开始复制
- 主服务器挂了再开，原有的从服务器依然存在

## 复制原理

- 主服务器收到从服务器的同步消息后，把rdb发给从服务器，每次写操作都同步从服务器

- 全量复制
- 增量复制

## 薪火相传

- 主管一个从，从管n个从，往下形成一个树形结构

## 反客为主

- slaveof no one
- 主机挂掉，可以把它的某一从机上位变成新的主服务器

## 哨兵模式

- 反客为主自动版
- /myredis里创建 sentinel.conf文件sentinel monitor mymaster ip名 端口号 n
  - mymaster是起的别名
  - n为至少有多少个哨兵同意迁移的数量
- redis-sentinel /myredis/sentinel.conf
- 原来的主机变成新主机的从服务器
- 选举规则：
  - .conf中的优先级：slave-priority（有的写成replica-priority)越小越高
  - 偏移量最大
  - runid最小
- java中使用：
  - 在连接池中修改



# 8

# 集群

- 扩容？并发写分摊？
- （原）代理
- （现）无中心化集群
- （在配置文件中加以下）
- cluster-enabled yes 打开集群模式
- cluster-config-file <文件名nodes-xxxx.conf\> 设置节点配置文件名
- cluster-node-timeout 15000 设定节点失联时间
- cd /opt/redis-6.2.1/src
- redis-cli --cluster create --cluster-replicas 1 参与集群的ip
  - 1表示用最简单的方式生成集群
- 集群方式连接：redis -cli -c -p 6379
- cluster nodes 查看当前情况

## 集群操作

- 插槽：slots，一个集群16384个插槽，会通过key计算决定把k放入哪个主机
- 无法一次插入多个kv
  - mset k1 v1 k2 v2 不行
  - mset k1{name}v1 k2{name}v2 可以，根据name计算插槽
- cluster keyslot <key\> 计算key的插槽值
- cluster getkeyslot <slot\><count\> 返回count个slot槽中的键
- cluster countkeysinslot <插槽值\>
  - 只能看自己插槽范围内的值

## 故障恢复

- 有哨兵模式
- cluster-require-full-coverage
  - yes：一对主从挂，整个集群挂
  - no：一对主从挂，只有该插槽数据无法使用无法存储

## 集群的Jedis开发

```java
public static void main(String[] args) {

    HostAndPort hostAndPort = new HostAndPort("192.168.232.131", 6379);
    JedisCluster jedisCluster = new JedisCluster(hostAndPort);
    
    jedisCluster.set("k1","v1");
    String k1 = jedisCluster.get("k1");

    System.out.println(k1);
    jedisCluster.close();
    
}
```



# 9

# 应用问题

## 缓存穿透

- 问题：应用服务器压力激增+redis命中率降低+一直查询数据库，db崩溃（redis和db中都没有想要的数据）

- 对空值缓存，过期时间短
- 设置可访问名单bitmap存可访问id
- 采用布隆过滤器
- 实时监控，设置黑名单

## 缓存击穿

- 问题：数据库访问压力瞬时增加+redis没有大量key过期+redis正常运行
- 原因：某个热门key过期
- 预先设置热门数据，加大key存在的时长
- 实时调整
- 使用锁：只有拿到锁才能访问数据库

## 雪崩问题

- 问题：数据库压力大服务器崩溃
- 原因：极少时间dalaingkey集中过期
- 多级缓存
- 锁或队列
- 设置更新标志更新缓存
- 将缓存失效时间分散开

## 分布式锁

- setnx <key\> <value\> 上锁
- del <key\> 释放锁
- 设置过期时间
- set <key\> <value\> nx ex <过期时间\> 既上锁又设置过期时间

- （没太理解应用场景）
- 误删问题：每次上锁把value设成一个uuid，释放锁的时候先判断uuid和自己的是否一样
- 还是会误删（没有进行原子性操作）：LUA脚本

# 10

# Redis6新功能

## ACL

- acl list 展示用户和权限
- cal cat string 展示指令
- acl setuser <user\>
- acl whoami 查看当前谁在操作
- acl setuser mary on >password~cached:* +get
  - 开启用户mary，有密码，key是cached，操作是get
- auth mary password 切换到mary

## 工具支持Cluster

- 继承ruby环境







































