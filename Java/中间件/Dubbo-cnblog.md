# Dubbo

[TOC]

# 01

## 相关概念

- 集群和分布式
  - 干的活完全一样 - > 集群
  - 很多人干一个活的不同部分 -> 分布式
- 分布式架构：在垂直架构的基础上，将公共业务模块抽取出来，作为独立的服务，供其他调用者消费，以实现服务的共享和重用
  - RPC：远程过程调用

![image-20231101091603710](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231104195625647-1419299491.png)

- SOA架构：面相服务的架构
  - ESB：企业服务总线，提供服务的交互和治理

![image-20231101091627167](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231104195625269-748345181.png)

- 微服务

![image-20231101091931559](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231104195624906-746022762.png)



## Dubbo概述

- Java RPC框架，提供RPC方案和SOA服务治理方案

![image-20231101093711350](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231104195624485-648646107.png)



# 02

## 快速入门

- 一个工程里，俩模块，web依赖service，web能独立启动

### 改造service

- pom里加dubbo等依赖
- 加tomcat插件
- 打包方式改为war
- 去掉spring的service注解，改为apache的service注解，将该类提供的服务对外发布，将访问地址注册到注册中心中
- 在配置文件中加dubbo和ZooKeeper（注册中心）的配置
  - 配置项目名称
  - 配置注册中心地址
  - 把spring的包扫描改成dubbo包扫描

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
      xmlns:mvc="http://www.springframework.org/schema/mvc"
      xmlns:context="http://www.springframework.org/schema/context"
      xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd
         http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

<!--   <context:component-scan base-package="com.xiaoyangii.service"/>-->
   <dubbo:application name="dubbo-service"/>
   <dubbo:registry address="zookeeper://192.168.2.5:2181"/>
   <dubbo:annotation package="com.xiaoyangii.service.impl"/>
</beans>
```

- webapp包（复制dubbo-web，删掉里面springmvc部分）

### 改造web

- 删掉对service的依赖
- 删掉web.xml对spring配置文件的加载
- **建立service.UserService接口（与service模块对应一致）**
- 注入方式从@Autowired改成@Reference（远程注入）

```java
/*
    1. 从zookeeper注册中心获取userService的访问url
    2. 进行远程调用RPC
    3. 将结果封装为一个代理对象。给变量赋值
 */
```

- xml配置同上
- 端口冲突

```xml
<dubbo:application name="dubbo-web" >
    <dubbo:parameter key="qos.port" value="33333"/>
</dubbo:application>
```

### 改造web2

- 解决上面加粗的问题
- 建一个interface模块，里面放service.UserService
- 让web和service模块都依赖interface模块



# 03

## Dubbo高级特性

### Dubbo-admin

- 从注册中心中获取提供者，消费者进行配置管理
- 安装：github搜，然后把配置文件里ZooKeeper改到本机
- 在根目录下powershell执行 mvn clean package

## 报错：dubbo-admin-server ................................. FAILURE

- 在cmd里面：命令改为mvn clean package -Dmaven.test.skip=true

## 报错：java.lang.IllegalStateException: zookeeper not connected, the address is: zookeeper://192.168.2.5:2181/org.apache.dubbo.config.ConfigCenterConfig?check=true&config-file=dubbo.properties&group=dubbo&highest-priority=false&namespace=dubbo&timeout=30000

- 解决不了了，黑马换课不良人，新课用dubbo3和springboot



## 第一个dubbo程序细节

- protocl端口：为了防止端口冲突，可以把端口号设为“-1”，会自动使用可用端口

- consumer调用的实际上是iml的**代理对象**，对consumer屏蔽网络通信过程进行数据传输



## SpringBoot整合Dubbo

### 梳理

- provider：![6db46973c189912e5f9c43820adbd0c](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231104195623902-1987611512.jpg)

- consumer：![4fe7153c3527f322ec119190328d651](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231104195623425-341661211.png)




### 整合

- 依赖

```xml
<dependency>
    <groupId>org.apache.dubbo</groupId>
    <artifactId>dubbo-spring-boot-starter</artifactId>
    <version>3.2.4</version>
</dependency>
```

- 配置

- ```yml
  spring:
    application:
      name: COMSUMMER01
  dubbo:
    protocol:
      name: dubbo
      port: -1
  ```

- 注解
  - provider里放@DubboService
  - 启动类里放@EnableDubbo
  - consumer需要注入的地方放@DubboReference(url="....")

### 注解

- @EnableDubbo：扫描Dubbo注解，扫描当前包及其子包
  - @DubboComponentScan(basePackages={"。。。。"})：扫描指定路径包
  - 也可在配置文件中用dubbo.scan.base-packages指定

- @DubboService：Spring会将它创建成对象，并发布成Dubbo服务
  - 为了保证兼容性，最好也加@Service
- @DubboReference注解：为consumer注入远端服务的代理对象



# 04

## DubboRPC的直连

- 无需注册中心的接入
- 三要素：协议，通信方式，序列化

## 序列化

![8e59eb60c7bd2c4671172f98c62217d](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231104195622656-1730970155.png)



### Kryo序列化

- 导入依赖
- 配置文件：dubbo.protocol.serialization: kryo
- 注解：@DubboReference 在url后面直接加?serialization= kryo即可



# 05

## 地址缓存

- 第一次调用本地后会缓存到本地，后面调用就不会再访问

## 超时与重试

- 设超时时间，无法访问就自动断开
  - 在@DubboService(timeout =3000, retires = 0)
  - 后面是重试次数

## 多版本

- 灰度发布
- version属性设置多版本
- 在service和Reference里分别指定即可

## 负载均衡

- 在Reference里加loadbalance


## 集群容错

## 服务降级

- 在@Reference里用mock指定

