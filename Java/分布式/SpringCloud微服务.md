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



### 最佳实践

- 每个想用item-service中queryItemByIds的人都需要写一个client，代码重复编写，修改也不方便
  - 方法一：可以在item-service中写dto和client模块，然后需要用它的人就引入pom坐标！
  - 缺点：项目结构变复杂
  - 方法二：单独提一个api模块，然后里面集成所有的client和dto

- 这里采用方法二
- 注意：如果单独提出来一个模块，它没有启动类，是无法被扫描到的
  - 可以在引用它的模块的启动类上，加@EnableFeignClients(basePackages = "com.hmall.api.client")

### 日志

- 只有在日志级别为debug时才会输出

- 四级（在yml里定义）

  - none 默认
  - basic 记录请求方法、url、响应状态码、执行时间
  - headers 请求和响应头信息
  - full 请求响应体、元数据

- 用法：

  - 定义一个bean，返回日志级别

    ```java
    public class DefaultFeignConfig {
    
        @Bean
        public Logger.Level feignLoggerLevel(){
            return Logger.Level.FULL;
        }
    
    }
    ```

  - 在@FeignClient中加Configuration（局部）

  - （或）加在@EnableFeignClient上（全局）

    ```java
    defaultConfiguration = DefaultFeignConfig.class
    ```

# 03

## 网关

- 两大问题
  - 服务端拆分后地址过多，前端不知道请求谁
  - 用户身份校验和用户信息获取
- 网关作用：路由、转发、身份校验
  - 比如设置网关端口为8080不变，前端编写代码的时候就只用请求网关即可
  - 网关在注册中心拉取服务
- 常用：Spring Cloud Gateway
- 区别nginx：
  - Spring Cloud Gateway主要是前后端之间，它更方便做一些功能拓展，比如用户身份校验等
  - nginx是用户到前端工程的网关

## 网关路由

- 引入依赖

```xml
<dependencies>
    <!--common-->
    <dependency>
        <groupId>com.heima</groupId>
        <artifactId>hm-common</artifactId>
        <version>1.0.0</version>
    </dependency>
    <!--网关-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-gateway</artifactId>
    </dependency>
    <!--nacos discovery-->
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
    </dependency>
    <!--负载均衡-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-loadbalancer</artifactId>
    </dependency>
</dependencies>
```

- 配置yml文件

```yml
spring:
  application:
    name: gateway
  cloud:
    nacos:
      server-addr: 192.168.150.101:8848
    gateway:
      routes:
        - id: item # 路由规则id，自定义，唯一
          uri: lb://item-service # 路由的目标服务，lb代表负载均衡，会从注册中心拉取服务列表
          predicates: # 路由断言，判断当前请求是否符合当前规则，符合则路由到目标服务
            - Path=/items/**,/search/** # 这里是以请求路径作为判断规则
          filters:
            - AddRequestHeader=vkllajkahkfajlhknm,ahifhkah #添加请求头 
        - id: cart
          uri: lb://cart-service
          predicates:
            - Path=/carts/**
```

### 路由属性

- 路由断言
- 路由过滤器：可以对当前请求进行操作（如增加请求头，改变请求路径等）

### 路由过滤

- filters
- defalt-filters:

## 网关登录校验

### 网关请求处理流程

![image-20231204145114391](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231204145114391.png)

- 登录校验
  - 在NettyRoutingFilter前做
- 将用户信息传递给微服务
  - 原：ThreadLoacal
  - 现在：加载请求头中
- 微服务之间传递信息

### 自定义过滤器

- GatewayFilter：默认不生效，在yml中配置，配置到哪个路由下就在哪生效
- GlobalFilter：全局过滤器

### GlobalFilter

- 参数
  - ServerWebExchange：保存请求上下文，包含整个过滤器链中的共享数据，例如request、response等
  - GatewayFilterChain：调用下一个过滤器
  - Mono 回调函数，实现非阻塞

- 定义

```java
@Component
public class MyGlobalFilter implements GlobalFilter {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        //TODO 模拟登录逻辑
        ServerHttpRequest request = exchange.getRequest();
        HttpHeaders headers = request.getHeaders();
        System.out.println("---------{}"+headers);
        return chain.filter(exchange);
    }
}
```

- 优先级：Ordered

```java
@Component
public class MyGlobalFilter implements GlobalFilter , Ordered {
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        //TODO 模拟登录逻辑
        ServerHttpRequest request = exchange.getRequest();
        HttpHeaders headers = request.getHeaders();
        System.out.println("---------{}"+headers);
        return chain.filter(exchange);
    }

    @Override
    public int getOrder() {
        return 0;//越小越先，可以为负
    }
}
```

### 实现登录校验

```java
@Component
@RequiredArgsConstructor
public class AuthGlobalFilter implements GlobalFilter, Ordered {

    private final AuthProperties authProperties;

    private final JwtTool jwtTool;
    //springBoot提供的一个路径匹配器
    private final AntPathMatcher antPathMatcher = new AntPathMatcher();

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {

        ServerHttpRequest request = exchange.getRequest();
        //判断是否需要登录拦截
       if(isExclude(request.getPath().toString()))
       {
           return chain.filter(exchange);
       }
        //拿到、校验、解析token
        List<String> headers = request.getHeaders().get("authorization");
       String token=null;
       if(headers!=null&&!headers.isEmpty()){
           token=headers.get(0);
       }

        Long userId = null;
        try {
            userId = jwtTool.parseToken(token);
        } catch (Exception e) {
            //拦截，设置响应状态码为401，返回
            ServerHttpResponse response = exchange.getResponse();
            response.setStatusCode(HttpStatus.UNAUTHORIZED);
            return response.setComplete();
        }

        if(userId!=null){
            System.out.println("userId = "+userId);
        }

        //传递信息

        return chain.filter(exchange);
    }

    private boolean isExclude(String path) {
        for(String pathPattern : authProperties.getExcludePaths()){
            if(antPathMatcher.match(pathPattern,path)){
                return true;
            }
        }
        return false;
    }

    @Override
    public int getOrder() {
        return -1;
    }
}
```



### 实现网关到微服务之间的信息传递

- 网关处

  ```java
  //传递信息
  ServerWebExchange newExchange = exchange.mutate()
          .request(builder -> builder.header("user-info", userInfo))
          .build();
  
  return chain.filter(newExchange);
  ```

- 拦截器：拦截请求，取出用户信息，放入threadlocal

  - 写在common中

  ```java
  public class UserInfoInterceptor implements HandlerInterceptor {
  
      @Override
      public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
  
          String userInfo = request.getHeader("user-info");
  
          if(!StrUtil.isNotBlank(userInfo)){
              UserContext.setUser(Long.valueOf(userInfo));
          }
  
          return true;
      }
  
  //最后还要清理掉threadlocal中的信息
      @Override
      public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
          UserContext.removeUser();
      }
  }
  ```

  - 配置

  ```java
  @Configuration
  public class MvcConfig implements WebMvcConfigurer {
  
      @Override
      public void addInterceptors(InterceptorRegistry registry) {
          registry.addInterceptor(new UserInfoInterceptor());
      }
  }
  ```

  - 被扫描：在配置类的META-INF包下的spring.factories中（第二行）

<img src="C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231204210131814.png" alt="image-20231204210131814" style="zoom:50%;" />

- 问题：网关底层不是mvc，但是又引入了common包，里面有webmvcConfig，会报错

  - 加条件

  ```java
  @Configuration
  @ConditionalOnClass(DispatcherServlet.class)
  public class MvcConfig implements WebMvcConfigurer {
  
      @Override
      public void addInterceptors(InterceptorRegistry registry) {
          registry.addInterceptor(new UserInfoInterceptor());
      }
  }
  ```



### 微服务间的信息传递

- 正常从网关发过来的请求，请求头里是有用户信息的；但是微服务之间用OpenFeign发送的请求头里没有

```java
@Bean
public RequestInterceptor userInfoRequestInterceptor(){
    return new RequestInterceptor() {
        @Override
        public void apply(RequestTemplate requestTemplate) {
            requestTemplate.header("user-info",Long.valueOf(UserContext.getUser()).toString());
        }
    };
}
```

## 小结

![image-20231204213459484](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231204213459484.png)

# 04

## 配置管理

- 提取通用的、重复的配置
- 原：每次业务变更都需要重启服务
  - 把易变动的、与业务服务有关的配置交给配置管理服务管理
  - 监听并推送配置变更
- 实现：Nacos

## 配置共享

- 添加配置到Nacos
  - 配置管理 -> 配置列表 -> 右上那个加号 ![image-20231204222930534](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231204222930534.png)

- 拉取共享配置

  - 流程

    <img src="C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231204223832136.png" alt="image-20231204223832136" style="zoom:30%;" />

  - 引入依赖

    ```xml
      <!--nacos配置管理-->
      <dependency>
          <groupId>com.alibaba.cloud</groupId>
          <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
      </dependency>
      <!--读取bootstrap文件-->
      <dependency>
          <groupId>org.springframework.cloud</groupId>
          <artifactId>spring-cloud-starter-bootstrap</artifactId>
      </dependency>
    ```

  -  bootstrap.yaml

    ```yaml
    spring:
      application:
        name: cart-service
      profiles:
        active: dev
      cloud:
        nacos:
          server-addr:  # nacos地址
          config:
            file-extension: yaml
            shared-configs:
              - data-id: shared-jdbc.yaml
              - data-id: shared-log.yaml
              - data-id: shared-swagger.yaml
    ```

  -  然后在Application.yaml中补充需要修改的地方

## 配置热更新

- 属性读取

  - 在config中写CartProperties读取配置类

- nacos中

  - dataId的格式：

    ```Plain
    [服务名]-[spring.active.profile].[后缀名]
    ```

  - ![image-20231204234744432](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231204234744432.png)













官方笔记：[‌⁤‌⁢﻿⁡⁡‍⁤⁡⁢‬‌‬⁢⁣⁡⁢‍‌⁡⁢⁣⁢⁣⁡‬‌⁤‍‍‌‬‍﻿‬⁤day03-微服务01 - 飞书云文档 (feishu.cn)](https://b11et3un53m.feishu.cn/wiki/R4Sdwvo8Si4kilkSKfscgQX0niB)
