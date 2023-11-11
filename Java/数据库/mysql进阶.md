# Mysql进阶

[TOC]

# 01

# 事务

- 要么全成功，要么全失败
- ![image-20231109214847831](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231109214847831.png)

## ACID

- 原子性
- 一致性
- 隔离性
- 持久性

## 并发事务

- 脏读：一个事务读到另一个事务还没有提交的数据
- 不可重复读：一个事务先后读取同一条记录，读取出来的数据不同
- 幻读：一个事务查询的时候没有对应数据行，但是插入数据时发现该数据行已经存在

## 隔离级别

![image-20231109213553075](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231109213553075.png)

![image-20231109213733961](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231109213733961.png)





# 02

- 连接层，服务层，引擎层，存储层

## 存储引擎

- show engines

### InnoDB

- 事务，外键，行级锁
- 高并发，完整性，一致性，多更新删除

### MyISAM

- 不支持事务，表级锁
- 读和插入为主

### MEMORY

- 临时表及缓存



# 03

# 索引

## 语法

![image-20231109224940068](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231109224940068.png)

## sql性能分析

- 查看sql执行频率
  - show global status like 'com\____\__'
  - 可以看当前数据库增删改查的执行频次

- 慢查询日志
  - ![image-20231109225856462](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231109225856462.png)

- profile详情

  - select @@profiling 查看是否打开
  - set profling = 1 打开

  - ![image-20231110125020531](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231110125020531.png)

- explain

  - 在sql语句前加 explain或desc

  - ![image-20231110130715900](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231110130715900.png)

    ![image-20231110130750818](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231110130750818.png)











































