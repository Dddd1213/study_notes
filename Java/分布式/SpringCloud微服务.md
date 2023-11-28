# SpringCloud微服务

[TOC]

# 01

## 概述

- ![image-20231108094136824](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231108094136824.png)

- ![image-20231108094247124](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231108094247124.png)

## 微服务

- 分布式架构方案
  - 单一职责
  - 面相服务
  - 自治
  - 隔离
- ![image-20231108102101411](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231108102101411.png)

- 注意版本兼容性！！！

![image-20231126170559826](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231126170559826.png)

## 测试高并发

- jmeter

### 依赖版本的管理

![image-20231126170751373](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231126170751373.png)





# 02

## 初始化（黑马商城）

- 还是下了navicat：[Navicat16.0.13免费永久激活破解教程 - 哔哩哔哩 (bilibili.com)](https://www.bilibili.com/read/cv17235251/#:~:text=6.在Patcher下选择navicat16的安装路径，点击Patch 7.点击KeyGen下的Generate按钮，会生成许可密钥，复制许可密钥 8.打开Navicat，粘贴许可密钥，点击激活 9.点击手动激活 10.复制请求码,11.将请求码复制到Request Code：下的文本框内 12.点击Generate Activation Code！)

- 前端在：flie\code\java\Java_learn\hmall-nginx
- 后端在：flie\资料\11微服务—资料\微服务01\资料
- 把cart和item分出来两个模块

## 远程调用

- 用RestTemplate发送http请求

  - 使用步骤：

    ![image-20231126220326973](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231126220326973.png)

  - 之前学过用httpclient，区别：

  ![image-20231126214624689](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231126214624689.png)

- @Autowared注入：spring不推荐
  - 推荐用构造函数注入
  - 可以在需要注入的参数前加final，然后用@RequiredArgsConstructor，这样就能防止不需要注入的参数被注入

- 这里：在cart-service中调用item-service的功能

```java
ResponseEntity<List<ItemDTO>> response = restTemplate.exchange(
        "http://localhost:8081/items?ids={ids}",
        HttpMethod.GET,
        null,
        new ParameterizedTypeReference<List<ItemDTO>>() {
        },
        Map.of("ids", CollUtil.join(itemIds, ","))
);
if(!response.getStatusCode().is2xxSuccessful()){
    //查询失败，直接结束
    return;
}
List<ItemDTO> items = response.getBody();
```



## 注册中心

![image-20231126223730168](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231126223730168.png)

## Nacos

- 需要一个数据库表存储nacos的数据

### 服务注册

- 引入依赖

```xml
<!--nacos 服务注册发现-->
<dependency>
    <groupId>com.alibaba.cloud</groupId>
    <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
</dependency>
```

- 配置Nacos

```yaml
spring:
  application:
    name: item-service # 服务名称
  cloud:
    nacos:
      server-addr: 192.168.150.101:8848 # nacos地址
```

- 按服务名称管理实例

### 服务发现

- 前两步同上

- 服务发现

  ```java
    List<ServiceInstance> instances = discoveryClient.getInstances("item-service");
          if(CollUtils.isEmpty(instances)){
              return;
          }
          //手写负载均衡
          ServiceInstance instance = instances.get(RandomUtil.randomInt(instances.size()));
  
  //        List<ItemDTO> items = itemService.queryItemByIds(itemIds);
  //        用restTemplate请求http
          ResponseEntity<List<ItemDTO>> response = restTemplate.exchange(
                  instance.getUri()+"/items?ids={ids}",
                  HttpMethod.GET,
                  null,
                  new ParameterizedTypeReference<List<ItemDTO>>() {
                  },
                  Map.of("ids", CollUtil.join(itemIds, ","))
          );
  ```



## OpenFeign

- 发送http请求的

### 快速入门

- 引依赖

```XML
  <!--openFeign-->
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-openfeign</artifactId>
  </dependency>
  <!--负载均衡器-->
  <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-loadbalancer</artifactId>
  </dependency>
```

- 在sevice的启动类上加注解

![image-20231127191109485](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231127191109485.png)

- OpenFeign客户端

```java
@FeignClient("item-service")
public interface ItemClient {

    @GetMapping("/items")
    List<ItemDTO> queryItemByIds(@RequestParam("ids") Collection<Long> ids);
}
```

- 使用
  - 补充：[@RequestParam使用-CSDN博客](https://blog.csdn.net/manonggeerdan/article/details/124125896?ops_request_misc=%7B%22request%5Fid%22%3A%22170108530816800226541229%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=170108530816800226541229&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-124125896-null-null.142^v96^pc_search_result_base2&utm_term=%40RequestParam&spm=1018.2226.3001.4187)

![image-20231127192116350](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231127192116350.png)

- （就不需要再用RestTemplate了）



























官方笔记：[‌⁤‌⁢﻿⁡⁡‍⁤⁡⁢‬‌‬⁢⁣⁡⁢‍‌⁡⁢⁣⁢⁣⁡‬‌⁤‍‍‌‬‍﻿‬⁤day03-微服务01 - 飞书云文档 (feishu.cn)](https://b11et3un53m.feishu.cn/wiki/R4Sdwvo8Si4kilkSKfscgQX0niB)
