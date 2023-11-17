# Spring Security

[TOC]

# 01

## 简介

- 安全框架
- 认证
- 授权

![image-20231116150537985](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231116150537985.png)

# 02

## 依赖

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```



# 03

## 认证

### 原理初探

- 过滤器链
- Authentication
- redis：每次认证都需要查询数据库浪费时间 -> 可以用userId作为key，用户详细信息作为value存入redis

## 思路分析

### 登录

- 自定义登录接口：调用providerManager；生成jwt；把用户信息存入redis
  - 需要对该接口进行放行：在securityconfig中实现configure(HttpSecurity http)类然后设置
  - 在loginServiceimpl中注入AuthenticationManager，调用authenticate

### 校验

- 定义jwt认证过滤器：获取token，解析并获得userId；从redis中获取key得到用户信息；存入**SecurityContextHolder**

### 查询

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

- domain里userLogin implements UserDetails，重写方法

  - UserDetails是Security框架中自动创建的用户接口，在没有用户或用户源相关配置时会自动创建User类实现该接口

- 注意：如果密码是铭文存储，前面要加{noop} 

  - 存密码前面都会加{标识}，校验时以什么方式加密会根据{标识}决定

  ![image-20231114113552049](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231114113552049.png)

  - 一般不用这种方式，会很麻烦，一般用Security提供的BCryptPasswordEncoder

  

## 认证过滤器

- extends OncePerRequestFilter
- 解析token
- 获得loginuser放入SecurityContextHolder

```java
UsernamePasswordAuthenticationToken authenticationToken =
        new UsernamePasswordAuthenticationToken(loginUser,null,null);
SecurityContextHolder.getContext().setAuthentication(authenticationToken);
```

- 配置拦截位置（在SecurityConfig中配置）
  - 放在UsernamePasswordAuthenticationFilter前面
  - configure方法中，http.

### 退出登录

```java
public ResponseResult logout() {
    UsernamePasswordAuthenticationToken authentication = (UsernamePasswordAuthenticationToken)SecurityContextHolder.getContext().getAuthentication();
    LoginUser loginUser = (LoginUser)authentication.getPrincipal();
    Long id = loginUser.getUser().getId();
    redisTemplate.delete("login:"+id);
    return new ResponseResult(200,"注销成功");
}
```



# 04

## 授权

- 在SecurityConfig上加@EnableGlobalMethodSecurity(prePostEnabled = true)
- 在需要权限校验的方法上加@PreAuthorize("hasAnyAuthority('test')")

```java
@PreAuthorize("hasAuthority('test')")
public String hello(){
   return "hello!";
}
```



### 封装权限信息

- UserDetailServiceImpl的loadUserByUsername方法中，由username查到了该用户的权限信息，封装到loginuser的permissions中
- 在loginuser的getAuthorities()方法中（重写），遍历，将permission的值传给SimpleGrantedAuthority authority 
- 注意：List<GrantedAuthority\> authorities不应该被序列化存储到redis中，加个注解@JSONField(serialize = false)



### 获取用户权限

#### RBAC权限模型

- 表设计
  - 用户表user：记录用户id，name
  - 权限表menu：记录每个操作需要的权限
  - 角色表role：一个角色对应一个权限组
  - 角色权限关联表role_menu：关联角色表和权限表，多对多
  - 用户角色关联表user_role：关联用户表和角色表，多对多



## 梳理一下和权限信息相关的地方

- UsersDetailServiceImpl这个替换的是Security模型中跟数据库挨最近的那一块，把它改成从我们自己的数据库中查询信息

  - 这个地方有个查询该用户的权限信息并封装进userDetails中

  ![image-20231115171739887](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231115171739887.png)

- LoginUser类继承了UserDetail，里面放了User user和permissions（权限信息列表），这里将上面传到LoginuserUser中的permission传入到Security框架中的authorities中，并重写了getAuthorities方法
  - 授权！！！

![image-20231115171911640](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231115171911640.png)

- JwtAuthenticationTokenFilter拦截器中，继承了OncePerRequestFilter方法（确保不会一次请求拦截多次重复操作），这里重写了doFilterInternal方法，在里面解析jwt令牌，并把登录者的信息存入redis和Security框架中
  - 认证！！！

![image-20231115172622872](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231115172622872.png)





# 05

## 自定义失败处理

- **需求：**认证失败或者是授权失败的情况下也能**和我们的接口一样返回相同结构的json**，这样可以让前端能对响应进行统一的处理。要实现这个功能我们需要知道SpringSecurity的异常处理机制：
- 在SpringSecurity中，如果我们在认证或者授权的过程中出现了异常会被ExceptionTranslationFilter捕获到。在ExceptionTranslationFilter中会去判断是认证失败还是授权失败出现的异常。
  - 如果是认证过程中出现的异常会被封装成AuthenticationException然后调用AuthenticationEntryPoint 对象的方法去进行异常处理。
  - 如果是授权过程中出现的异常会被封装成AccessDeniedException然后调用AccessDeniedHandler对象的方法去进行异常处理。

- 方式：
  - 自定义**AuthenticationEntryPoint**和**AccessDeniedHandler**
  - 在配置类中配置给SpringSecurity



# 06

## 跨域

### springboot跨域

```java
@Configuration
public class CorsConfig implements WebMvcConfigurer {
 
    @Override
    public void addCorsMappings(CorsRegistry registry) {
      // 设置允许跨域的路径
        registry.addMapping("/**")
                // 设置允许跨域请求的域名
                .allowedOriginPatterns("*")
                // 是否允许cookie
                .allowCredentials(true)
                // 设置允许的请求方式
                .allowedMethods("GET", "POST", "DELETE", "PUT")
                // 设置允许的header属性
                .allowedHeaders("*")
                // 跨域允许时间
                .maxAge(3600);
    }
}
```

### Security跨域

![image-20231115211457193](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231115211457193.png)



# 07

## 自定义权限校验方法

- 定义一个类，加@Component
- 在需要验证权限的方法上加@PreAuthorize("@ex.hasAuthority('system:dept:test')")
  - ex是前面类加入容器时指定的名字

## 基于配置的权限控制

- 在配置类中.antMatchers("/hello").hasAuthority("权限名")

## CSRF

- 跨站请求伪造
- 前后端分离的需要关闭

## 认证成功处理器

- AuthenticationSuccessHandler
- 需要在配置类配置登录表单

## 认证失败处理器

## 登出成功处理器



