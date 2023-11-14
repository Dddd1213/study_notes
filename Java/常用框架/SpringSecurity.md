# Spring Security

[TOC]

# 01

## 简介

- 安全框架
- 认证
- 授权

# 02

## 依赖

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

## 认证

### 原理初探

- 过滤器链
- Authentication
- redis：每次认证都需要查询数据库浪费时间 -> 可以用userId作为key，用户详细信息作为value存入redis

## 思路分析

### 登录

- 自定义登录接口：调用providerManager；生成jwt；把用户信息存入redis
  - 需要对该接口进行放行
- 自定义userDetailService：在这个实现类中查询数据库

### 校验

- 定义jwt认证过滤器：获取token，解析并获得userId；从redis中获取key得到用户信息；存入SecurityContextHolder

### 代码实现

- 依赖

  ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-redis</artifactId>
  </dependency>
  <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>fastjson</artifactId>
      <version>1.2.76</version>
  </dependency>
  <dependency>
      <groupId>io.jsonwebtoken</groupId>
      <artifactId>jjwt</artifactId>
      <version>0.9.1</version>
  <dependency>
  ```

- service实现UserDetailsService，重写loadUserByUsername，改为从自己的数据库中查询，返回userDetails包装类

- domain里implements UserDetails，重写方法

- 注意：如果密码是铭文存储，前面要加{noop} 

  - 存密码前面都会加{标识}，校验时以什么方式加密会根据{标识}决定

  ![image-20231114113552049](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231114113552049.png)

  - 一般不用这种方式，会很麻烦，一般用Security提供的BCryptPasswordEncoder

  





































