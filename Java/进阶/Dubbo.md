# Dubbo

[TOC]

# 01

## 相关概念

- 集群和分布式
  - 干的活完全一样 - > 集群
  - 很多人干一个活的不同部分 -> 分布式
- 分布式架构：在垂直架构的基础上，将公共业务模块抽取出来，作为独立的服务，供其他调用者消费，以实现服务的共享和重用
  - RPC：远程过程调用

![image-20231101091603710](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231101091603710.png)

- SOA架构：面相服务的架构
  - ESB：企业服务总线，提供服务的交互和治理

![image-20231101091627167](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231101091627167.png)

- 微服务

![image-20231101091931559](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231101091931559.png)

## Dubbo概述

- Java RPC框架，提供RPC方案和SOA服务治理方案

![image-20231101093711350](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231101093711350.png)



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





































