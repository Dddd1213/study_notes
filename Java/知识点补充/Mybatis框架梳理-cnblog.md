# Mybatis框架梳理

[TOC]

## 01

- Resources中
  - mybatis-config.xml：用于连接数据源（配置url等连接信息），决定事务作用域和控制方式
    - mappers标签中需要加载所有的sql映射文件
  - UserMapper.xml：sql映射文件，用来写增删改查具体sql语句
    - namespace：名称空间，这里起到一个标识这个类的作用
    - resultType：对应的返回结果的类型（全路径）
  
- 启动：

  - 执行sql的statement里写的是名称空间.方法名
  - 最后sqlSession.close()

  ![image-20231105111045984](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231105170200308-1405171914.png)



## 02 加入mapper代理

- UserMapper接口

  - 与sql映射文件**同名**，并将mapper接口与sql映射文件放在同一目录下

    ![image-20231105111824792](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231105170159901-1864678329.png)

  - 在mapper接口中定义方法，方法名是sql映射文件中sql语句的id，参数类型和返回值需要一致，方法的参数用@Param指定，需要和sql映射文件中对应
  - 参数传递底层：多个参数会封装成map集合

- UserMapper.xml：设置namespace为Mapper接口的全限定名

- mybatis-config.xml：可以改用包扫描的方式，就不用一个个写

  ![image-20231105112525013](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231105170159483-1753126679.png)启动：

  - 区别：不用再通过xml文件，而是获取mapper接口的代理对象，然后用代理对象调用方法
  - 好处：调用方法方便，不用一个个指定名字

  ![image-20231105112048850](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231105170158979-2033450105.png)

  

## 04 MybatisX插件

- 出现小鸟标志
- xml和接口方法相互跳转
- 根据接口方法生成statement



## 03 注解开发

- 将一些简单的查询语句直接用注解写到方法上

![image-20231105151629422](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231105170158549-1975574736.png)



## 04 Spring整合Mybatis

- 整合完后，就不需要mybatis-config.xml文件了！

- 导依赖

  - spring操作jdbc需要

  ![image-20231105152955835](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231105170158046-1879731846.png)

  - spring整合Mybatis需要

    ![image-20231105153037481](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231105170157598-1254153178.png)

- SpringConfig类中
  - @Configuration表示是配置类
  - @ComponentScan("com.itheima") 扫所有把该管理的bean管理上
  - @PropertySource("classpath:jdbc.properties")，扫描，把配置类中的信息加载到JdbcCinfig中
  - @Import("{JdbcConfig.class,MybatisConfig.class}")

- JdbcConfig类

  - 配置第三方数据源并给bean管理

- MybatisConfig类

  - 在里面设置方法返回设置好的SqlSessionFactoryBean对象（把什么连接信息url全部set进去）并给bean管理
  - 设置方法返回映射扫描配置的对象

  ![image-20231105155016505](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231105170157087-1354660856.png)

![image-20231105154603767](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231105170156524-1697972366.png)



## 05 SpirngBoot整合Mybatis

- 起步依赖

![image-20231105161844078](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231105170156059-1898814591.png)

- application.yml中

  - 写url等数据库信息

  ![image-20231105161956211](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231105170155556-1884956380.png)

- BookDao中（就是原来的UserMapper接口）

  - 加**@Mapper**注解：创建代理
  - （也可以不加，然后在启动类上加@MapperScan("com.itheima.dao.BookDao")，也是扫描然后创建代理）
  - （以前创建代理是在MybatisConfig中用MapperScanner扫描的）

  ![image-20231105162325878](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231105170155007-858444005.png)



## 06 MybatisPlus

- 引入起步依赖

![image-20231105164918729](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231105170154064-1424359110.png)

- 在UserMapper中继承BaseMapper<T\>

- 另：在UserService中继承ServiceImpl<UserMapper, User\>，实现IService接口