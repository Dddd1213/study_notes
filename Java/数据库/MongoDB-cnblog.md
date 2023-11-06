# MongoDB

[TOC]

# 1

# 概述

## 应用场景

- 高并发
- 海量数据
- 高可扩展性高可用性

- 具体场景
  - 社交，朋友圈等
  - 游戏中装备，积分等
  - 物流，实时更新的数据信息
  - 物联网，智能设备信息，日志
  - 直播，用户信息，点赞等
- 特点：
  - 数据量大
  - 读写操作频繁（2000-3000的QPS）
  - 价值较低，**对事务要求不高**
  - 成本较低

## 体系结构

- 数据库-  集合（表） - 文档（行）

## 启动

- > mongod --dbpath=..\data\db

- mongod -f 配置文件位置
- mongod -config 配置文件位置
- 默认：27017
- mongosh 连接数据库
- show dbs 显示已有的数据库

## linux安装

- 放在 usr/soft下

- （已解决：没运行起来，不知道哪有问题）

### child process failed, exited with 51

- 法一：删掉data中的mongod.lock文件，然后修复，没解决
- 法二：注释掉fork：也没解决，显示什么已吐核

参考：[详解在Linux中安装配置MongoDB_mongodb -4.4.17 版本 linux机器为8核15g 最优的mongodb.conf配置_fx67ll的博客-CSDN博客](https://blog.csdn.net/PirateRaccoon/article/details/121475610?ops_request_misc=&request_id=&biz_id=102&utm_term=linux下mongodb版本&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-2-121475610.142^v96^pc_search_result_base2&spm=1018.2226.3001.4187)

- 解决：参考这个教程安装一点问题没有[Linux安装MongoDB_往西巷的博客-CSDN博客](https://blog.csdn.net/qq_61567609/article/details/130250367?ops_request_misc=%7B%22request%5Fid%22%3A%22169832558216800192263370%22%2C%22scm%22%3A%2220140713.130102334.pc%5Fall.%22%7D&request_id=169832558216800192263370&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-1-130250367-null-null.142^v96^pc_search_result_base2&utm_term=linux中mongodb的下载&spm=1018.2226.3001.4187)
- 位置在/usr/local/mongodb

# 2

# 基本使用

## 数据库

- 新建的数据库会保存在内存而不是磁盘中
- db 显示当前数据库
- 默认库
  - admin：存放数据库用户和权限
  - local：部署集群用，如果放在local中就不会被复制
  - config：mongo用于分片设置时，config在内部使用保存分片相关信息

- dp.dropDatabase()

- use DatabaseName 若存在则切换，不存在就创建



## 集合（表）

- db.createCollection("myc") 显示创建
- show collections
- db.集合名.drop()



## 文档

### 增

- BSON格式（二进制JSON）
- 单个插入

```
db.collection.insert(
 <document or array of documents>,
 {
 writeConcern: <document>,
 ordered: <boolean>
 }
 )
 //举例，像名为comment的集合中插入
 db.comment.insert({"articleid":"100000","content":"今天天气真好","userid":"1001","nickname":"Rose","createdatetime":new Date(),"likenum":NumberInt(10),"state":null})
```

- 批量插入

```
db.collection.insertMany(
 [ <document 1> , <document 2>, ... ],
 {
 writeConcern: <document>,
 ordered: <boolean>
 }
 )
```

- trycatch：插入失败会终止，但已成功的数据无法回滚

```
try {
 db.comment.insertMany([
 {"_id":"1","articleid":"100001","content":"我们不应该把清晨浪费在手机上，健康很重要，一杯温水幸福你我
他。","userid":"1002","nickname":"相忘于江湖","createdatetime":new Date("2019-08
05T22:08:15.522Z"),"likenum":NumberInt(1000),"state":"1"},
 {"_id":"2","articleid":"100001","content":"我夏天空腹喝凉开水，冬天喝温开水","userid":"1005","nickname":"伊人憔
悴","createdatetime":new Date("2019-08-05T23:58:51.485Z"),"likenum":NumberInt(888),"state":"1"},
 ]);
 } catch (e) {
 print (e);
 }
```

### 查

- 查询所有

```
db.comment.find()
或
db.comment.find({})
```

- 按条件查询

```
db.comment.find({userid:'1003'})
```

- 按条件且只返回一条

```
db.comment.findOne({userid:'1003'})
```

- 投影查询：返回部分字段（1是查，0是排除在外）

```
 >db.comment.find({userid:"1003"},{userid:1,nickname:1})
 { "_id" : "4", "userid" : "1003", "nickname" : "凯撒" }
 { "_id" : "5", "userid" : "1003", "nickname" : "凯撒" }
```

## 改

- 更新语句

```
db.collection.update(query, update, options)
 //或
db.collection.update(
 <query>,
 <update>,
 {
 upsert: <boolean>,
 multi: <boolean>,
 writeConcern: <document>,
 collation: <document>,
 arrayFilters: [ <filterdocument1>, ... ],
 hint:  <document|string>        
}
 )
```

- 参数

![image-20231025160110857](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231027001513887-986289581.png)

- 默认：覆盖修改

```
db.comment.update({_id:"1"},{likenum:NumberInt(1001)})
```

- 局部修改 $set:

```
 db.comment.update({_id:"2"},{$set:{likenum:NumberInt(889)}})
```

- 批量修改{multi:true}

```
 //默认只修改第一条数据
db.comment.update({userid:"1003"},{$set:{nickname:"凯撒2"}})
 //修改所有符合条件的数据
db.comment.update({userid:"1003"},{$set:{nickname:"凯撒大帝"}},{multi:true})
```

- 原值基础上加减：$inc

```
//对3号数据的点赞数，每次递增1
db.comment.update({_id:"3"},{$inc:{likenum:NumberInt(1)}})
```



## 删

- 语法：

```
db.集合名称.remove(条件)
```

- 删全部

```
 db.comment.remove({})
```



## 分页查询

- 统计查询

```
db.collection.count(query, options)
```

![image-20231025191855564](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231027001513313-727213943.png)

- 分页列表，跳过NUMBER条查接下来的NUMBER2条

```
db.COLLECTION_NAME.find().limit(NUMBER2).skip(NUMBER)
```

- 排序查询：1为升序，-1为降序；要写在find后面

```
db.COLLECTION_NAME.find().sort({KEY:1})
或
db.集合名称.find().sort(排序方式)
```



## 更多查询

- 带正则表达式的模糊查询（强大！）（js语法）

```
db.collection.find({field:/正则表达式/})
或
db.集合.find({字段:/正则表达式/})
//包含“开水”
db.comment.find({content:/开水/})
```

- 比较查询

```
db.集合名称.find({ "field" : { $gt: value }}) // 大于: field > value
 db.集合名称.find({ "field" : { $lt: value }}) // 小于: field < value
 db.集合名称.find({ "field" : { $gte: value }}) // 大于等于: field >= value
 db.集合名称.find({ "field" : { $lte: value }}) // 小于等于: field <= value
 db.集合名称.find({ "field" : { $ne: value }}) // db.comment.find({likenum:{$gt:NumberInt(700)}})等于: field != value
 
 db.comment.find({likenum:{$gt:NumberInt(700)}})
```

- 包含查询

![image-20231025193104261](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231027001512914-514329301.png)

- 条件连接查询

```
$and:[ {  },{  },{ } ]
db.comment.find({$and:[{likenum:{$gte:NumberInt(700)}},{likenum:{$lt:NumberInt(2000)}}]})

$or:[ {  },{  },{   } ]
db.comment.find({$or:[ {userid:"1003"} ,{likenum:{$lt:1000} }]})

```



# 3

# 索引

- 单字段，复合索引，其他

## 索引操作

- 查看

```
db.collection.getIndexes()
```

- 创建索引
  - unique：是否唯一
  - name：索引名称
  - （复合索引就加多个key）

```
db.collection.createIndex(keys:1或-1, options)

 db.comment.createIndex({userid:1,nickname:-1})
 {
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 2,
        "numIndexesAfter" : 3,
        "ok" : 1
 }
```

- 移除索引：可以通过索引名称或索引规范文档指定索引。若要删除文本索引，请指定 索引名称

```
 db.collection.dropIndex(index)
```

- 移除所有索引

```
db.collection.dropIndexes()
```



## 索引的使用

- 执行计划：查看建立的索引是否有效（也可在compass里看）

```
db.collection.find(query,options).explain(options)
```

- FRTCH

  

# 4

# 案例：文章评论

黑马笔记：[mongodb_base.pdf](file:///D:/flie/资料/9、MongoDB基础到进阶/资料-mongodb基础到进阶/01_MongoDB用起来-快速上手/01_讲义/mongodb_base.pdf)



# 5

# 副本集

## 两种类型

- 主节点
- 次节点

## 三种角色

- 主要成员：写
- 副本成员：不写，可读（需要配置），可选举
- 仲裁者：有选举权没有被选举权。仲裁者不维护数据集。 仲裁者的目的是通过 响应其他副本集成员的心跳和选举请求来维护副本集中的仲裁。

## 副本集的创建

- 创建三个结点和，配置文件，db，log
- 启动结点服务
- 使用客户端命令连接结点（最好是主）
- 初始化 initiate
- 查看配置内容 rs.conf()
- 查看状态 rs.status()
- 添加副本从节点 rs.add(host, arbiteonly)
- 添加仲裁结点 rs.addArb(host)

## 测试

- 从节点不能读？

  - rs.slaveOk(true) 作为奴隶结点且允许读（false可取消）

    

## 主节点的选举原则

- 选举条件
  - 主节点故障
  - 主节点网络不可达
  - 人工干预
- 选举规则
  - 票数最高，一半以上赞同
  - 如果满足上述，数据新的节点获胜（通过log）

- 票数：可以设置priority来设置自己票的权重



## 故障测试

### 副本节点故障

- kill再启动，还是会跟随主节点（redis的不会，需要再次设置为从）

### 主节点故障

- 重新选取
- 再起启动降级为从节点

### 仲裁和主都故障

- 从仍然是从，因为副本集里有3个，从只得1票，不满足“大多数”

### 仲裁和从都故障

- 主自动降级为副本节点



## Compass连接副本集

- 若云服务器：
  -  var config = rs.config();  config.members[0].host="180.76.159.126:27017";rs.reconfig(config)
- ![image-20231026223928062](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231027001512394-1208716961.png)

- compass连从节点后，若要执行“写操作”，会自动切到主节点然后写入到副本集中
- ![image-20231026224125139](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231027001511852-430187007.png)



##  SpringDataMongoDB连接副本集

- ```
  mongodb://host1,host2,host3/articledb?
   connect=replicaSet&slaveOk=true&replicaSet=副本集名字
  ```

  

# 6

# 分片集群

- 解决系统增长：垂直扩展（单个机器）和水平扩展（多个机器）
- 组件
  - 分片（存储）
  - mongos（路由）
  - config servers（调度的配置）

![image-20231026225419123](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231027001511221-460066369.png)

## 分片集群构建

- 要先搭router再搭其他的
- （所有的的配置文件都直接放到sharded_cluster的相应的子目录下面，默认配置文件名字： mongod.conf）

![image-20231026230401843](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231027001510420-1609694704.png)

- 分片角色

![image-20231026230636651](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231027001509321-1303581038.png)

- 配置节点副本集在router的配置文件中写出
- 添加分片副本集： sh.add("ip:port")
- rs.status()
- sh.status()
- 开启分片：sh.enableSharding("库名")，sh.shardCollection("库名.集合名", {"key": 分片规则})
- 分片策略：hash；值范围规则（写1或-1都可）
- 改默认开启分片的数据库尺寸（eg. 改为1M）：
  - user config
  - db.settings.save({_id: "chunksize", value:1})

# 7

# 安全认证

- 角色+权限

## 添加用户和权限

- db.createUser({"user": "myroot",roles:["root"]})
  - user为后面为用户名（myroot），可以随便取
  - roles:["role":"root", "db":"admin"]
- db.system.users.find()
  - 查看创建了哪些用户
- db.dropUser()

## 认证测试

- ```
   //切换到admin
   > use admin
   //密码输错
  > db.auth("myroot","12345")
   Error: Authentication failed.
   0
   //密码正确
  > db.auth("myroot","123456")
   1
  ```

  

## 服务端开启认证

- db.shutdownServer()
- 法一：启动时加 --auth
- 法二：在配置文件中加

```
security:
  #开启授权认证
  authorization: enabled
```

- 登录：db.auth("用户名", "密码")
- 连接时直接认证：在最后加 -u myroot -p 123456 -- authenticationDatabase 指定连接的库（必须！）

### Spring

```
spring:
 #数据源配置
data:
 mongodb:
 # 主机地址
#      host: 180.76.159.126
 # 数据库
#     database: articledb
 # 默认端口是27017
# port: 27017
 #帐号
#username: bobo
 #密码
#   password: 123456
 #单机有认证的情况下，也使用字符串连接
uri: mongodb://bobo:123456@180.76.159.126:27017/articledb
```



## 集群环境安全认证

- openssl rand -base64 90 -out ./mongo.keyfile
  - 生成key文件
  - 放在每一个副本节点上
- 在配置文件中指定

```
 security:
 #KeyFile鉴权文件
keyFile: /mongodb/replica_sets/myrs_27017/mongo.keyfile
 #开启认证方式运行
authorization: enabled
```

- 关闭并重新启动副本集

### Spring

- 使用 'username:password@hostname/dbname' 格式，'username'为用户名，'password' 为密码

```
spring:
 #数据源配置
data:
 mongodb:
 #副本集有认证的情况下，字符串连接
uri: 
mongodb://bobo:123456@180.76.159.126:27017,180.76.159.126:27018,180.76.159.126:2
 7019/articledb?connect=replicaSet&slaveOk=true&replicaSet=myrs
```



## 分片集群

- 每一个节点都放一个keyfile，且必须有读的权限
- localhost可以不用验证权限创建用户









黑马笔记：[mongodb_advance.pdf](file:///D:/flie/资料/9、MongoDB基础到进阶/资料-mongodb基础到进阶/02_MongoDB用起来-集群安全/01_讲义/mongodb_advance.pdf)

网盘链接：https://pan.baidu.com/s/17DU8d42_iSyTeC8dPIFz3w&pwd=5678
