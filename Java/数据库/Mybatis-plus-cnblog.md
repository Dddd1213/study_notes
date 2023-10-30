# Mybatis-plus

[TOC]

# 快速入门

- **@TableField**的常用场景
  - 成员变量名与数据库字段名不一致（会自动驼峰转下划线）
  - 成员变量名以is开头，且是bool值（经过反射处理后会去掉is）
  - 成员变量名和数据库关键字冲突（需要前后加`进行转义字符处理）
  - 成员变量不是数据库字段

- 常用配置

```yml
mybatis-plus:
  type-aliases-package: com.itheima.mp.domain.po #别名扫描包
  mapper-locations: "classpath*:/mapper/** /*.xml" #Mapper.xml文件地址，默认值
  configuration:
    map-underscore-to-camel-case: true #是否开启下划线和驼峰的映射
    cache-enabled: false #是否开启二级缓存
  global-config:
    db-config:
      id-type: assign_id # id为雪花算法生成
      update-strategy: not_null #更新策略:只更新非空字段
```



# 核心功能

## 条件构造器

- 各种Wrapper

- LambdaWrapper
  - 避免字符串硬编码
  - 把get函数传过去，程序可以通过反射机制获取要操作的字段名

## 自定义SQL

![image-20231016212620725](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231019203729575-1269701580.png)

## service接口

- 查一个用get
- 查多个用list
- 分页page
- lambdaQuery() 写复杂条件查询，不用new

- 接口extends IService<User\>
- 实现类extends ServiceImpl<UserMapper, User\> implements IUserService
- swagger地址：后面加doc.html



报错：Dependency 'com.github.xiaoymin:knife4j-openapi2-spring-boot-starter:' not found

- 去ProjectStructure，Libraries里找，看这个：[引入knife4j依赖时报错Could not transfer artifact com.github.xiaoymin:knife4j-spring-boot-starter:pom:2.0.7_could not find artifact com.github.xiaoymin:knife4-CSDN博客](https://blog.csdn.net/QYHuiiQ/article/details/109754718)



## 实战

### swagger

- knife4j配置：

  ```yaml
  knife4j:
    enable: true
    openapi:
      titile: 用户管理接口文档
      description: "用户管理接口文档"
      email: zhanghuyi@cast.cn
      concat: 虎哥
      url: https://www.itcast.cn
      version: v1.0.0
      group:
        default: 
          group-name: default
          api-rule: package
          api-rule-resources:
            -com.ithema.mp.controller
  ```

- @ApiOperation：描述接口的操作。
- @ApiParam：描述单个接口参数。

### 其他

- dto：用于在不同层（例如，业务逻辑层和表示层、服务层和数据访问层，返回给微服务的等等）之间传输数据的对象。

- po：存放数据库实体

- vo：返回给前端的

- 把dto拷贝到po

  ```java
  BeanUtil.copyProperties(userDTO,User.class)
      copytoList
  ```

  

- ```java
  @RequiredArgsConstuctor
  
  private final IUserService userService；
  ```

  


## Lambda

- 前面可以写条件

```java
//不为空才传
lambdaQuery().like(name!=null,User::getUsername,name)
```

- 注意：更新完成后最后要加个.update()
- 注意2：如果要修改的话，考虑多线程，需要加锁（乐观锁/悲观锁）



## Iservice的批量新增

- （举例：提交1000个对象）

- 预编译

- 法一：普通for逐条加入，提交1000次

- 默认mp：编译出1000条sql语句，一次性提交（减少网络请求）

- 需求：编译出一条，像这样：

  ![image-20231019114418856](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231019203728864-1101387356.png)

- 方式：在jdbc后面连一个rewriteBatchedStatements = true




# 扩展功能

## 代码生成器

- 查文档

- mybatisplus插件

  ![image-20231019115435793](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231019203728211-1049937555.png)

![image-20231019115443829](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231019203727627-828473009.png)

## 静态工具

- 不用注入，解决循环依赖的情况
- 直接用Db.调用（用baomidou的）
- eg.根据id查用户，查用户的同时查出用户对应的地址

![image-20231019185410406](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231019203727073-918174200.png)

- eg.根据id**批量查询**用户对应的地址

  - 先根据ids在user表中查用户
  - 再根据查到的userId在address表中查地址

  ![image-20231019190413594](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231019203726456-1153793160.png)

![image-20231019190450105](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231019203725916-400349302.png)



## 逻辑删除



## 枚举处理器

- 问题一：用户状态可读性差

  - 解决：建一个枚举类，然后在里面写状态和desc增强可读性；在User类中，把Int status直接改成UserStatus枚举类型

- 问题二：User和数据库中的status类型不对应（一个UserStatus一个Int）

  - 解决：在枚举类中对应数据库值的地方加@EnumValue注解

  ![image-20231019191351103](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231019203725250-109023992.png)

  - 然后配置文件让它生效

  ```
  mybatis-plus:
  	configuration:
  		default-enum-type-handler:com.baomidou.mybatisplus.core.handlers.MybatisEnumTypeHandler
  ```

  - @JsonValue：决定给前端的返回值

  ![image-20231019191750215](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231019203724812-1611061591.png)

## JSON处理器

- 问题：数据库中存json字段info，java中用string接，如果想中途从json中取出部分制定内容，不好取

- 解决：建一个UserInfo类，存；然后用JacksonTypeHandler转

- 实现：autoResultMap和typeHandler（两个地方！！）

  ![image-20231019192923142](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231019203724300-33106384.png)



## 插件

### 分页插件

- ![image-20231019193455703](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231019203723684-1293025400.png)

- ![image-20231019194445809](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231019203723169-1996757582.png)

- 构建分页条件的步骤比较繁琐且通用：提出来

![image-20231019202246158](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231019203722539-1669814226.png)

- 需求一

  - 通过泛型和可变参数增加通用性
  - 注意：记得给类中的属性先赋个初值，保证它不为空

  ![image-20231019202646085](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231019203721708-668175559.png)













































