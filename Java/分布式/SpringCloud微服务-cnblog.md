# SpringCloud微服务

[TOC]

# 01

## 概述

- ![image-20231108094136824](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221454241-70341471.png)

- ![image-20231108094247124](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221453572-2053102729.png)

## 微服务

- 分布式架构方案
  - 单一职责
  - 面相服务
  - 自治
  - 隔离
- ![image-20231108102101411](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221452892-394018501.png)

- 注意版本兼容性！！！

![image-20231126170559826](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221452262-594476630.png)

## 测试高并发

- jmeter

### 依赖版本的管理

![image-20231126170751373](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221451639-1223360273.png)





# 02

## 初始化（黑马商城）

- 还是下了navicat：[Navicat16.0.13免费永久激活破解教程 - 哔哩哔哩 (bilibili.com)](https://www.bilibili.com/read/cv17235251/#:~:text=6.在Patcher下选择navicat16的安装路径，点击Patch 7.点击KeyGen下的Generate按钮，会生成许可密钥，复制许可密钥 8.打开Navicat，粘贴许可密钥，点击激活 9.点击手动激活 10.复制请求码,11.将请求码复制到Request Code：下的文本框内 12.点击Generate Activation Code！)

- 前端在：flie\code\java\Java_learn\hmall-nginx
- 后端在：flie\资料\11微服务—资料\微服务01\资料
- 把cart和item分出来两个模块

## 远程调用

- 用RestTemplate发送http请求

  - 使用步骤：

    ![image-20231126220326973](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221451024-327849860.png)

  - 之前学过用httpclient，区别：

  ![image-20231126214624689](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221450576-1746717636.png)

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

![image-20231126223730168](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221450005-482243390.png)

## Nacos（重要）

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



## OpenFeign（重要）

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

![image-20231127191109485](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221449384-1662706294.png)

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

![image-20231127192116350](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221448780-940740643.png)

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

![image-20231204145114391](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221448119-2095277494.png)

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

![image-20231204213459484](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221447440-1850528916.png)

# 04

## 配置管理

- 提取通用的、重复的配置
- 原：每次业务变更都需要重启服务
  - 把易变动的、与业务服务有关的配置交给配置管理服务管理
  - 监听并推送配置变更
- 实现：Nacos

## 配置共享

- 添加配置到Nacos
  - 配置管理 -> 配置列表 -> 右上那个加号 ![image-20231204222930534](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221446932-1285177815.png)

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

  - ![image-20231204234744432](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221446443-719675645.png)

- （意思就是在nacos中的配置文件改变，不用重启项目，它能热加载）



# 05

## 雪崩问题

- 某个服务故障导致整个链路中所有的微服务都不可用
- 解决：服务降级
  - 请求限流，降低并发
  - 线程隔离：通过限定每个业务能使用的线程数量将故障业务隔离，避免故障扩散
  - 服务熔断：由熔断器统计请求异常比例或慢调用比例，如果超出阈值则熔断该业务，拦截该接口的请求；熔断期间所有请求快速失败，走fallback逻辑

- 服务保护技术

![image-20231205003505408](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221445979-1604978729.png)

## Sentinel

- 启动控制台：

  ```Shell
  java -Dserver.port=8090 -Dcsp.sentinel.dashboard.server=localhost:8090 -Dproject.name=sentinel-dashboard -jar sentinel-dashboard-1.8.6.jar
  ```

- 配置启动项：[启动配置项 · alibaba/Sentinel Wiki (github.com)](https://github.com/alibaba/Sentinel/wiki/启动配置项)

- 查看控制台

  - 默认用户名密码都是sentinel

  ![image-20231205123604092](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221445262-396795025.png)

- 导入依赖

  ```XML
  <!--sentinel-->
  <dependency>
      <groupId>com.alibaba.cloud</groupId> 
      <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
  </dependency>
  ```

- 配置控制台

- 簇点链路：单机调用链路，指一次请求进入服务后每一个被sentinel监控的资源链，默认sentinel会监控springMVC的每一个endpoint（http接口）。默认会把请求路径识别成一个簇点资源的名称。

  - 打开请求方式前缀

    ```YAML
    spring:
      cloud:
        sentinel:
          transport:
            dashboard: localhost:8090
          http-method-specify: true # 开启请求方式前缀
    ```

  - 就会变成这样

    ![image-20231205124155385](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221444611-738379523.png)

### 限流

![image-20231205124647348](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221444093-22062804.png)

- jmeter：模拟高并发

  - 点开D:\Software\Code\apache-jmeter-5.4.1\bin中的jmeter.bat即可运行
  - ![image-20231205131415436](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221443649-1116148480.png)

  - 可以看到通过是6，拒绝是4

    ![image-20231205131749665](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221442929-408069346.png)

### 线程隔离

- 限制每个服务能够使用的线程数量，即使它出现了故障，也不会把tomcat的资源耗尽

- 配置在feign的远程调用上 - > 服务调用者方

  - 这里是配置在了cart-service里，不是hm-api上

- openfeign整合sentinel

  ```YAML
  feign:
    sentinel:
      enabled: true # 开启feign对sentinel的支持
  ```

- 加完之后，cart-service调用item-service的请求就会被监控，簇点链路上就会出现/item-service/items，然后对它进行流控设置
  - 设置并发线程数的单机阈值
  - 区别：QPS和并发线程数，可能一个线程能处理两个请求，那么此时并发线程数为1，QPS为2



### FallBack

- 比如请求超出阈值，被拒绝的部分可以自定义拒绝策略，就叫fallback

- fallbackClass：无法对远程调用的异常做处理
- fallbackfactory：可以、、、、，通常选择这种
  - 每一个client都有一个自己的fallbackfactory

- 先定义一个fallback类

```java
@Slf4j
public class UserClientFallbackFactory implements FallbackFactory<ItemClient> {

    @Override
    public ItemClient create(Throwable cause) {
     
        return new ItemClient() {
            @Override
            public List<ItemDTO> queryItemByIds(Collection<Long> ids) {
                //查询异常返回空集合
                log.error("远程调用查询商品异常",cause);
                return Collections.emptyList();
            }
        };
    }
}
```

- 把它定义成bean

```java
@Bean
public ItemClientFallbackFactory itemClientFallbackFactory(){
    return new ItemClientFallbackFactory();
} 
```

- 在client接口上声明它

```java
@FeignClient(value = "item-service",fallbackFactory = ItemClientFallbackFactory.class)
public interface ItemClient {

    @GetMapping("/items")
    List<ItemDTO> queryItemByIds(@RequestParam("ids") Collection<Long> ids);

}
```



### 服务熔断

- 就是把状态差的服务暂时先停掉，不要这个功能，不让他影响整体的运行（类似加载一个页面，某一个数据没有被加载出来，但整个页面没有卡住）
- 三个状态：
  - closed：达到失败阈值（失败比例过高、慢请求比例过高），就转open
  - open：快速失败，一定时间后转half-open
  - half-open：熔断时间结束，尝试放行一次请求，失败open，成功closed

- 设置

  ![image-20231205151812097](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221442022-1031065254.png)



### 持久化

- 花钱官方保存

- 保存在nacos配置文件中（重）

- 引依赖（配在了cart-service中）

  ```xml
  <!--        sentinel持久化配置-->
          <dependency>
              <groupId>com.alibaba.csp</groupId>
              <artifactId>sentinel-datasource-nacos</artifactId>
              <version>1.8.6</version>
  ```

- 在nacos中创建，json中写规则

  ![image-20231205153334071](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221441567-199106008.png)

- 在配置文件中拉取

  ```yml
  spring:
    cloud:
      sentinel:
        transport:
          dashboard: localhost:8090
        http-method-specify: true # 开启请求方式前缀
        datasource: 
          ds2: #配置文件的数据源名称，随便起ds1,ds2,siajofh,saidj
            nacos:
              server-addr: 192.234.132.123:8848 #nacos地址
              data-id: degrade.json #刚在nacos中建的文件名
              group-id: DEFAULT_GROUP
              data-type: json
              rule-type: degrade #降级
  ```

- 缺点

  - 在nacos中手写容易出错
  - 这么配之后，如果再到控制台修改，不会再写到nacos中去



# 06

## 分布式事务

- 多个服务的多个事务必须同时成功或失败

## seata

- 集大成者！
- 三个角色
  - TC：事务协调者，维护状态，协调全局事务的提交或回滚
  - TM：事务管理器，定义全局事务的**范围**，哪开始哪结束
  - RM：资源管理器，和TC通信

![image-20231205154944164](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221441042-439145481.png)

## seata的使用

- 准备数据库表（持久化）

- 准备配置文件

  - 把seata拷贝到虚拟机root目录
  - docker启动容器

- 看nacos的服务列表中有seata就是注册好了

  ![image-20231205172906408](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221440428-40129874.png)



## 微服务整合seata

- 依赖

  ```XML
  <!--统一配置管理-->
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-nacos-config</artifactId>
    </dependency>
    <!--读取bootstrap文件-->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-bootstrap</artifactId>
    </dependency>
    <!--seata-->
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-seata</artifactId>
    </dependency>
    <!--sentinel-->
    <dependency>
        <groupId>com.alibaba.cloud</groupId>
        <artifactId>spring-cloud-starter-alibaba-sentinel</artifactId>
    </dependency>
  ```

- 配置，让微服务找到TC地址

  - 在nacos中添加一个共享配置，避免重复配置的麻烦

  ```YAML
  seata:
    registry: # TC服务注册中心的配置，微服务根据这些信息去注册中心获取tc服务地址
      type: nacos # 注册中心类型 nacos
      nacos:
        server-addr: 192.168.150.101:8848 # nacos地址
        namespace: "" # namespace，默认为空
        group: DEFAULT_GROUP # 分组，默认是DEFAULT_GROUP
        application: seata-server # seata服务名称
        username: nacos
        password: nacos
    tx-service-group: hmall # 事务组名称
    service:
      vgroup-mapping: # 事务组与tc集群的映射关系
        hmall: "default"
  ```

![image-20231205205827617](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221439920-1425599838.png)



## XA模式

- 分阶段提交

![image-20231205211157041](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221439172-1244086001.png)

- 缺点：openfeign串行执行，要等另一个事物完成，资源锁死，性能差；依赖关系型数据库

- 步骤

  - 修改application.yml（每个参与事物的微服务）（也可直接在nacos中seata的共享配置中加）

    ```yml
    seata：
    	data-source-proxy-mode: XA
    ```

  - 给发起全局事务的入口方法添加@GlobalTransactional

  - Todo：是只需要在入口处加还是所有地方都要加



## AT模式（主）

- 在提交前记录一份快照undo-log（空间换时间）

![image-20231205212739778](https://img2023.cnblogs.com/blog/3299940/202312/3299940-20231205221438326-1305172105.png)

- 缺点：不是强一致，是最终一致，不符合ACID，但性能比XA好，所以多用这个

- 使用：
  - 创建undo_log表（用资料中seata-at.sql）
  - 修改application.yml为AT
  - 给发起全局事务的入口方法添加@GlobalTransactional

- 尽可能避免出现分布式事务！

官方笔记：[‌⁤‌⁢﻿⁡⁡‍⁤⁡⁢‬‌‬⁢⁣⁡⁢‍‌⁡⁢⁣⁢⁣⁡‬‌⁤‍‍‌‬‍﻿‬⁤day03-微服务01 - 飞书云文档 (feishu.cn)](https://b11et3un53m.feishu.cn/wiki/R4Sdwvo8Si4kilkSKfscgQX0niB)
