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

## 验证索引效率

- 先用没有索引的字段进行查询，看耗时
- 然后对该字段创建索引，看耗时



## 索引失效情况

- 如果索引了多列(联合索引)，要遵守最左前缀法则。最左前缀法则指的是查询从索引的最左列开始，并且不跳过索引中的列。如果跳跃某一列，索引将**部分失效**(后面的字段索引失效)。

- 范围查询（出现> <这种）右边的列索引失效
  - 尽量使用>=
- 对索引字段进行运算操作，会失效
- 字符串没加引号，索引失效
- 尾部模糊匹配，不失效；头部模糊，失效
- or连接，如果前有索引，后没有索引，索引失效
- 若评估不使用索引更快，索引失效



## 索引使用

- sql提示

  ![image-20231112145403041](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231112145403041.png)

- 覆盖索引


- 前缀索引
  - 截取前几个 substring(email,1,10)，从第1个开始截，截10个

![image-20231112150743554](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231112150743554.png)

- 单列索引和联合索引




# 04

# sql优化

## 插入数据

- 批量插入（不超过1000条）
- 手动开启和提交事务
- 主键顺序插入
- 使用load

![image-20231112171718349](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231112171718349.png)



## 主键优化

- 页分裂
- 页合并
- 不要使用uuid或如身份证号的自然主键
  - 长度较长，提高IO压力
  - 乱序，插入可能导致页分裂等，效率降低

## order by

- using filesort：性能低，
- using index: 效率高，直接根据索引返回排序好的数据

## group by



## limit

- 覆盖索引+子查询 

## count

- 尽量用count(*) 官方做了优化

## update

- update没有索引的字段时，加的就不是行锁了而是表锁



# 05

## 视图

- 一张虚拟存在的表 

![image-20231112183807931](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231112183807931.png)

- 往视图中插入数据，实际上会插入到基表中



### 检查选项

- cascaded：检查当前及递归往前的where
- local：检查当前及递归往前有with检查选项的where

### 更新

- 必须保证视图中的行与基础表中一对一（如果使用了聚合函数之类的就不能）



## 存储过程

- 将sql语句进行封装和重用 
- （类似java中写个方法然后调用）

![image-20231112191756812](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231112191756812.png)



# 06 

# 锁



# 07

# InnoDB



# 08

# 





































