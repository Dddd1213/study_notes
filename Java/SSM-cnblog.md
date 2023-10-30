# SSM

[TOC]

# 1

# Spring

# 1.1 

## Spring Framework

- 基础 最底层架构

# 1.2

## IoC  DI

- IOC容器对象创建初始化
- 被管理的对象叫Bean
- 把dao，service和建立bean之间的依赖都放在一个springconfig配置文件中进行
- 使用 `@Controller` 注解时，Spring会自动扫描并管理带有该注解的类，无需显式配置；在配置文件中配置 bean 需要手动指定要管理的类，并在配置文件中进行详细配置，灵活性更高，但需要更多的手动工作。

### IoC

1. 导入maven依赖
2. 定义Spring管理的类
3. 创建SpringConfig配置文件，配置对应的类
4. 初始化IoC容器并从容器中获取Bean

### DI

1. 提供对应的set方法

   - 在创建`bookService` bean时，它会检测到`setBookDao`方法，并将在配置文件中配置的`bookDao` bean的引用传递给这个方法，从而完成依赖注入。

   - 试了下，在init前就调用了set方法

     ![pipt63Q.png](https://z1.ax1x.com/2023/10/13/pipt63Q.png)

2. 在xml配置文件中配置server与dao的关系

### 理解

- 感觉这个像把耦合部分放到配置文件中了，如果dao修改，则不需要再修改service中的代码，而是修改配置文件，这样比较方便？

### 基础配置

- @Scope注解：控制单例/多例对象；在配置文件标签中加prototype也可
- 默认为单例，一般只有封装实体的域对象需要用多例



### bean

#### 构造方法（常用）

- Spring创建bean的时候调用的是**无参**的构造方法

#### 静态工厂（了解）

- 静态工厂知识补充：http://t.csdnimg.cn/EUKXU
- 标签中配工厂类名加**factory-method**
- 缺点：<bean id="userFactory" class="com.itheima.factory.UserDaoFactory"/\>仅仅为了下一个bean使用，很多余且麻烦

#### 实例工厂(重要)

- 实例工厂的方法是非静态的，创一个实例工厂的对象，然后调用里面的dao方法
- 创一个UserDaoFactoryBean类实现FactoryBean
- 控制单例/非单例：在UserDaoFactoryBean类增加一个isSingleton

```xml
   <!--方式一：构造方法实例化bean-->
    <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"/>

    <!--方式二：使用静态工厂实例化bean-->
   <bean id="orderDao" class="com.itheima.factory.OrderDaoFactory" factory-method="getOrderDao"/>

    <!--方式三：使用实例工厂实例化bean-->
    <bean id="userFactory" class="com.itheima.factory.UserDaoFactory"/>

    <bean id="userDao" factory-method="getUserDao" factory-bean="userFactory"/>

    <!--方式四：使用FactoryBean实例化bean-->
    <bean id="userDao" class="com.itheima.factory.UserDaoFactoryBean"/>
```



### Bean生命周期

- 关闭容器
  - ctx.close() 暴力关容器
  - ctx.registerShutdownHook(); 注册关闭钩子函数，在虚拟机退出之前回调此函数，关闭容器
- 生命周期控制
  - 法一：自定义init和destroy，然后配置
  - 法二：接口控制

##### 生命周期

- 初始化容器
  - 创建对象（内存分配)
  - 执行构造方法
  - 执行属性注入( set操作)
  - 执行bean初始化方法

- 使用bean
  - 执行业务操作

- 关闭/销毁容器
  - 执行bean销毁方法

### 依赖注入

#### setter注入（重要）（常用）

##### 引用类型

- 在bean中定义引用类型属性并提供需要注入对象的set方法
- 在配置中使用property标签name（set**方法名**），ref（ref指明的是哪一个bean）

##### 简单类型

- 在配置中使用property标签name（属性名），value（值）

#### 构造器注入

- 把set方法改成构造器
- 配置文件中用constructor-arg，name写**形参名称**，ref写bean名
- 解耦合
  - 指定type，index

#### 使用场景

- 强制依赖用构造器
- 可注可不注用setter

举例说明：http://t.csdnimg.cn/zcDHZ



### 自动装配

- 仍需提供set方法

- autoware

- ```xml
  <bean id="bookService" class="com.itheima.service.impl.BookServiceImpl" autowire="byType"/>
  ```

- 按名称装配：bean中有一个id和类中的变量名耦合（不推荐）
- 自动装配无法对简单类型进行依赖注入



### 集合注入

- 请看vcr

  ```xml
  <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl">
          <!--数组注入-->
          <property name="array">
              <array>
                  <value>100</value>
                  <value>200</value>
                  <value>300</value>
              </array>
          </property>
          <!--list集合注入-->
          <property name="list">
              <list>
                  <value>itcast</value>
                  <value>itheima</value>
                  <value>boxuegu</value>
                  <value>chuanzhihui</value>
              </list>
          </property>
          <!--set集合注入-->
          <property name="set">
              <set>
                  <value>itcast</value>
                  <value>itheima</value>
                  <value>boxuegu</value>
                  <value>boxuegu</value>
              </set>
          </property>
          <!--map集合注入-->
          <property name="map">
              <map>
                  <entry key="country" value="china"/>
                  <entry key="province" value="henan"/>
                  <entry key="city" value="kaifeng"/>
              </map>
          </property>
          <!--Properties注入-->
          <property name="properties">
              <props>
                  <prop key="country">china</prop>
                  <prop key="province">henan</prop>
                  <prop key="city">kaifeng</prop>
              </props>
          </property>
      </bean>
  ```

  

### 管理第三方数据源



### 加载properties文件

[![piptr4S.png](https://z1.ax1x.com/2023/10/13/piptr4S.png)](https://imgse.com/i/piptr4S)

- 不加载系统属性：system-properties-mode="null"
- 多个配置文件
  - 直接在location后的引号里加逗号，加新的
  - location后面写"classpath\*:*.properties"（第一个\*表示不仅可以从当前包读，还能从它依赖的jar包读）



### 容器

#### 加载配置文件

- 加载类路径下的配置文件
- ....绝对路径...
- 加载多个：用，间隔就行

#### 获取bean的方式

- 名称
- 类型

#### BeanFactory

- ioc容器的顶层接口
- bean延迟加载





## 注解开发

- 在需要放入ioc容器的类上加@Component

  - @Component 不属于以下三类时用
  - @Controller 标注在控制器类型上 
  - @Service 标注在业务类型上
  - @Repository 标注在数据访问类上（与mybatis整合，用得少）

- 在配置文件中scan扫描

  ```
  <context:component-scan base-package="">
  ```

### 纯注解开发

- 新建一个配置类加@Configuration
- 加**@ComponentScan**({"com.example","com.itheima"}) 扫描指定包，把有注解的放到bean里
  - 多个包：大括号，逗号分隔
- main中配置文件名改成SpringConfig.class（SpringConfig是刚刚新建的配置类名）

### 生命周期

- @PostConstruct
- @PreDestroy

### 依赖注入

- @Autowired 按类型装配的！
  - 原理是暴力反射
- 若按名称装入：@Qualifier("名称")
- 简单类型注入：@Value("值")
- 配置文件：
  - 在SpringConfig类中 @PropertySource("配置文件路径")，*不被使用
  - @Value("${name}")

### 管理第三方bean

- 在config包下新建一个类

- 在SpringConfig类前加@Import("")导入该类

- @Bean 将方法的**返回值**交给IOC容器管理

  ```java
  @Bean
  public SAXReader saxReader(){
      return new SAXReader();
  }
  ```

#### 注入

- 简单类型：配置类成员变量用@Value给进去就行
- 引用注入：直接写在方法形参中！！自动（按类型）装配！



## 整合

### mybatis

- 核心：把配置文件改成配置类，造SqlSessionFactory

```java
 //定义bean，SqlSessionFactoryBean，用于产生SqlSessionFactory对象
    @Bean
    public SqlSessionFactoryBean sqlSessionFactory(DataSource dataSource){
        SqlSessionFactoryBean ssfb = new SqlSessionFactoryBean();
        ssfb.setTypeAliasesPackage("com.itheima.domain");
        ssfb.setDataSource(dataSource);
        return ssfb;
    }
    //定义bean，返回MapperScannerConfigurer对象
    @Bean
    public MapperScannerConfigurer mapperScannerConfigurer(){
        MapperScannerConfigurer msc = new MapperScannerConfigurer();
        msc.setBasePackage("com.itheima.dao");
        return msc;
    }
```



### junit



# 1.3

## AOP

- 面相切面编程（面相特定方法编程）：在不动原始代码的基础上进行功能增强；核心本质是**代理模式**
  - 打印对象得到com.itheima.dao.impl.BookDaoImpl@6913c1fb是因为它对tostring做了重写
  - 打印对象.getClass()得到的是class com.sun.proxy.$Proxy19
- 思路
  - 导入坐标
  - 制作连接点方法
  - 制作共性功能
  - 定义切入点
  - 在注解类加@EnableAspectJAutoProxy，让它知道是用注解开发AOP功能

```java
//通知类必须配置成Spring管理的bean
@Component
//设置当前类为切面类类
@Aspect
public class MyAdvice {

    //指定切入点
    @Pointcut("execution(void com.itheima.dao.BookDao.update())")
    private void pt(){};

    //绑定切入点和通知
    @Before("pt()")
    public void method(){
        System.out.println(System.currentTimeMillis());
    }
}
```

## 相关概念

- 连接点：JoinPoint 可以被AOP控制的方法
- 通知：Advice 被提取出的重复逻辑
- 切入点：PointCut 匹配连接点的条件
- 目标对象：Target 通知所应用的对象

## 切入点表达式

- 抽取（复用）

```java
@Pointcut("execution(* com.itheima.service.*.*(..))")
private void pt(){};
```

- execution

  - execution(访问修饰符? 返回值 包名.类名.? 方法名（方法参数) throws 异常?)    其中带?的可以省略
  - 通配符：*(1个任意)  ..（任意个任意） +
  - 尽量基于接口描述，增强拓展性

- @annotation(注解全类名)

  - 新建一个注解

  ```java
  @Retention(RetentionPolicy.RUNTIME)
  @Target(ElementType.METHOD)
  public @interface MyLog {
  }
  ```



## 通知类型

- **@Around 环绕**（重点）

  - 需要在参数里加ProceedingJoinPoint，并在方法体中写对原始操作调用的地方

  ```java
  public Object around(ProceedingJoinPoint pjp) throws Throwable {
      System.out.println("around before advice ...");
      //表示对原始操作的调用
      Object ret = pjp.proceed();
      System.out.println("around after advice ...");
      //如原始操作有返回值，则最后需要return出去（类型一致即可）
      return ret;
  }
  ```

- @Before 前置通知

- @After 后置通知

- @AfterReturning 返回后通知

- @AfterThrowing 异常后通知



## AOP通知获取数据

- 获取参数：

  - JoinPoint 获取连接点信息

  - @Around 通知只能用 ProcrrdingJoinPoint
  
  - 应用举例：输入的密码前后有多余的空格，可以用Around处理 去掉空格后再扔给dao那边查找

```java
    @Around("pt()")
    public Object around(ProceedingJoinPoint pjp) {
        Object[] args = pjp.getArgs();
        //可以对参数进行处理再传回去
 System.out.println(Arrays.toString(args));
        args[0] = 666;
        Object ret = null;
        try {
            ret = pjp.proceed(args);
        } catch (Throwable t) {
            t.printStackTrace();
        }
        return ret;
    }
```

- 获取返回值：环绕，返回后通知

  - AfterReturning：先定义一个用来接返回值的形参；若有JoinPoint，JoinPoint必须在前

  ```java
  //设置返回后通知获取原始方法的返回值，要求returning属性值必须与方法形参名相同
  @AfterReturning(value = "pt()",returning = "ret")
  public void afterReturning(JoinPoint jp,String ret) {
      System.out.println("afterReturning advice ..."+ret);
  }
  ```




# 1.4

## Spring事务

- 开完事务后，同成功同失败
- 在接口上加@Transactional配置当前接口方法具有事务
- 在Config里定义事务管理器

```java
@Bean
public PlatformTransactionManager transactionManager(DataSource dataSource){
    DataSourceTransactionManager transactionManager = new DataSourceTransactionManager();
    transactionManager.setDataSource(dataSource);
    return transactionManager;
}
```

- 在Spring的配置文件中写@EnableTransactionManagement开启注解式事务驱动


## 事务角色

- 事务管理员：发起方
- 事务协调员：加入方



## 事务属性

- rollbackFor 指定遇到某一类异常回滚
  - 若不设置 则只有Error和运行时异常才会回滚

- 成不成功都记录日志
  - try finally
  - Transactional(propagation = Propagation.REQUIRES_NEW)日志对应的事务单独隔出来一个全新的事务



# 2

# SpringMVC

- 和sevlet差不多，都是web层开发技术，但是更简单
- 版本和spring保持一致

## 步骤

- 导入springmvc和servlet坐标
- 定义Controller
- 创建springmvc和tomcat启动的配置文件



## bean加载控制

- 让spring别扫到springmvc中的bean：
  - spring扫全部，excludeFilters排除Controller
  - spring精准制定扫哪些包



## 请求

- @RequestMapping


- **get 传普通参数**

  - 写到形参上（要求名称相同）

- **post 传普通参数**

  - 请求参数在请求体body内
  - form-data 可以发表单和文件，另一个只发表单
  - 写到形参上（要求名称相同）

- 中文乱码？

  - 在ServletContainersInitConfig中设置过滤器

  ```java
  @Override
  protected Filter[] getServletFilters() {
      CharacterEncodingFilter filter = new CharacterEncodingFilter();
      filter.setEncoding("UTF-8");
      return new Filter[]{filter};
  }
  ```

- 名称不一致：@RequestParam手动映射

  - 不一致可通过@RequestParam手动映射

  ```java
  //post http://localhost:8080/method12?id=1&name=小小  
  @PostMapping("/method12")
  @ResponseBody
      public ApiResponse method12(@RequestParam("id") int id, @RequestParam("name")String name){
          String s = id + "" + name;
          System.out.println(s);
          return ApiResponse.ok().data(s);
      }
  ```


- **传pojo**
  - 若属性名一样，则可直接传
  - 若有引用属性：在传的时候写 引用对象.属性名（按嵌套层次结构）
- 传数组
  - 请求参数名与数组名一致，直接封装
- 传集合
  - 请求参数名与集合名一致，@RequestParam绑定关系
- json
  - 导json坐标
  - get发json数据：get/post，body，raw，json
  - springmvcConfig中加@EnableWebMvc
  - @RequestBody

- 日期参数

  - springmvcConfig中加@EnableWebMvc
  - @DataTimeFormat(pattern = "yyyy-MM-dd")

### 响应

```java
//响应页面/跳转页面
//返回值为String类型，设置返回值为页面名称，即可实现页面跳转
@RequestMapping("/toJumpPage")
public String toJumpPage(){
    System.out.println("跳转页面");
    return "page.jsp";
}

//响应文本数据
//返回值为String类型，设置返回值为任意字符串信息，即可实现返回指定字符串信息，需要依赖@ResponseBody注解
@RequestMapping("/toText")
@ResponseBody
public String toText(){
    System.out.println("返回纯文本数据");
    return "response text";
}

//响应POJO对象
//返回值为实体类对象，设置返回值为实体类类型，即可实现返回对应对象的json数据，需要依赖@ResponseBody注解和@EnableWebMvc注解
@RequestMapping("/toJsonPOJO")
@ResponseBody
public User toJsonPOJO(){
    System.out.println("返回json对象数据");
    User user = new User();
    user.setName("itcast");
    user.setAge(15);
    return user;
}

//响应POJO集合对象
//返回值为集合对象，设置返回值为集合类型，即可实现返回对应集合的json数组数据，需要依赖@ResponseBody注解和@EnableWebMvc注解
@RequestMapping("/toJsonList")
@ResponseBody
public List<User> toJsonList(){
    System.out.println("返回json集合数据");
    User user1 = new User();
    user1.setName("传智播客");
    user1.setAge(15);

    User user2 = new User();
    user2.setName("黑马程序员");
    user2.setAge(12);

    List<User> userList = new ArrayList<User>();
    userList.add(user1);
    userList.add(user2);

    return userList;
}
```





## REST风格

- 描述形式相似，通过POST，GET等**行为动作**区分对资源进行了何种操作


- 通常：
  - GET 查询
  - POST 新增
  - PUT 修改、更新
  - DELETE 删除

（迷迷糊糊的这一段....怎么找不到项目的启动类.....）

- @RestController = @Controller（用于标记一个类为处理HTTP请求的控制器）  + @ResponseBody（用于标记方法返回值将直接作为响应体返回给客户端；返回数据格式，如JSON、XML等）

### 步骤

- 根据操作行为选择http请求动作

- 设置请求参数（路径变量）

  - 在value中加参数的站位
  - @PathVariable

  

## 拦截器

- 在制定方法前后设置预先设定的方法
- 阻止原始方法的执行



# 3

# Maven

- 分模块开发
- 导入坐标：在分出去模块的pom.xml里有它的坐标 
- install：将被分出去的模块安装到本地仓库中

## 依赖传递



## 可选依赖与排除依赖

- 隐藏后不可依赖传递 <optional\>true<optional\>
- 排除依赖：<exclusions\>，隐藏别人的依赖



## 聚合

- 快速构建多个工程

- 原：先install再package

- 聚合工程：一个不具有业务功能的工程（一般为父工程）

  ![image-20230901002318526](https://z1.ax1x.com/2023/10/15/pi9TDkq.png)


## 继承

- 将公共依赖写在父工程中，子工程可以直接继承父工程的配置信息

- 只能单继承

  ![image-20230901000723500](https://z1.ax1x.com/2023/10/15/pi97eNq.png)

## 版本锁定

- 在父工程中用<dependencyManagement\>统一管理版本，子工程无须再指定版本

- 自定义属性

  ![image-20230901001810112](https://z1.ax1x.com/2023/10/15/pi97m40.png)



## 配置文件加载属性

- 定义属性
- （配置文件中）引用属性
- 开启过滤

```xml
<properties>
   <spring.version>5.2.10.RELEASE</spring.version>
    <junit.version>4.12</junit.version>
    <--定义属性-->
	<jdbc.url>jdbc:mysql://127.0.0.1:3306/ssm_db</jdbc.url>
</properties>
 <build>
        <resources>
            <!--设置资源目录，并设置能够解析${}-->
            <resource>
               <directory>${project.basedir}/src/main/resources</directory>
                <filtering>true</filtering>
            </resource>
        </resources>
        </plugins>
    </build>
```

## 配置多环境

- id唯一
- maven install -P env_test

```xml
<!--配置多环境-->
<profiles>
    <!--开发环境-->
    <profile>
        <id>env_dep</id>
        <properties>
            <jdbc.url>jdbc:mysql://127.1.1.1:3306/ssm_db</jdbc.url>
        </properties>
        <!--设定是否为默认启动环境-->
        <activation>
            <activeByDefault>true</activeByDefault>
        </activation>
    </profile>
    <!--生产环境-->
    <profile>
        <id>env_pro</id>
        <properties>
            <jdbc.url>jdbc:mysql://127.2.2.2:3306/ssm_db</jdbc.url>
        </properties>
    </profile>
    <!--测试环境-->
    <profile>
        <id>env_test</id>
        <properties>
            <jdbc.url>jdbc:mysql://127.3.3.3:3306/ssm_db</jdbc.url>
        </properties>
    </profile>
</profiles>
```

- 跳过测试

  - 点闪电
  - mvn 指令 -D skipTests
  - pom文件

  ```xml
  <build>
      <plugins>
          <plugin>
              <artifactId>maven-surefire-plugin</artifactId>
              <version>2.12.4</version>
              <configuration>
                  <skipTests>false</skipTests>
                  <!--排除掉不参与测试的内容-->
                  <excludes>
                      <exclude>**/BookServiceTest.java</exclude>
                  </excludes>
              </configuration>
          </plugin>
      </plugins>
  </build>
  ```

  

## 私服

- 模拟了一台中央服务器
- 在exe处开cmd：nexus.exe /run nexus
- 访问：localhost:8081

- 仓库分类
  - 宿主仓库hosted：上传，自研+第三方
  - 代理仓库proxy：下载，连接中央仓库
  - 仓库组group：下载，简化下载

- 本地仓库访问私服

  - D:\Software\Code\apache-maven-3.8.1\conf
  - 配置本地仓库访问私服的权限
  - 配置url地址 镜像

- 私服资源上传

  - maven操作中 install然后deploy

  ```xml
  <!--配置当前工程保存在私服中的具体位置-->
  <distributionManagement>
      <repository>
          <id>itheima-release</id>
          <url>http://localhost:8081/repository/itheima-release/</url>
      </repository>
      <snapshotRepository>
          <id>itheima-snapshot</id>
          <url>http://localhost:8081/repository/itheima-snapshot/</url>
      </snapshotRepository>
  </distributionManagement>
  ```

# 4

# SpringBoot

- 自动配置
- 起步依赖
- 内置服务器等

## starter 起步依赖

- 变更：先排除tomcat依赖，再到jetty

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <exclusions>
            <exclusion>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-tomcat</artifactId>
            </exclusion>
        </exclusions>
    </dependency>

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jetty</artifactId>
    </dependency>
```



## 配置文件

### 参数配置化

- 用配置文件代替硬编码
- **@Value("${}")** 赋值

### 不同种类的配置文件

- properties 结构不清晰

  - 键值对

- yml / yaml 推荐

  - xx: xx ，不同层级缩进不同
  - 大小写敏感
  - 数值前面必须有空格，作为分隔符
  - 缩进时不能用TAB要用空格（idea会自动转换）
  - \# 表注释

  ```yml
  lesson: SpringBoot
  
  server:
    port: 80
  
  enterprise:
    name: itcast
    age: 16
    tel: 4006184000
    subject:
      - Java
      - 前端
      - 大数据
  ```

- xml 臃肿

  - 标签

- @ConfigurationProperties(prefix = "前缀") 可以批量将外部的属性配置注入到bean对象的属性中

  ```java
    //使用Environment封装全配置数据
      @Autowired
      private Environment environment;
  
  
        System.out.println(environment.getProperty("lesson"));
          System.out.println(environment.getProperty("server.port"));
          System.out.println(environment.getProperty("enterprise.age"));
          System.out.println(environment.getProperty("enterprise.subject[1]"));
  ```

  法二：

  ```java
  @Component
  //使用@ConfigurationProperties注解定义当前实体类读取配置属性信息，通过prefix属性设置读取哪个数据
  @ConfigurationProperties(prefix = "enterprise")
  public class Enterprise {
      private String name;
      private Integer age;
      private String tel;
      private String[] subject;
  ..........
  ```

  ```java
  @Autowired
      private Enterprise enterprise;
  System.out.println("---------------------");
          System.out.println(enterprise);
  ```

  

  - 添加依赖（可选）

  ```xml
      <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-configuration-processor</artifactId>
          </dependency>
  ```

- 自定义对象封装数据警告解决方案

```xml
<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
</dependency>
```



### 多环境开发配置

- 中间用三个横线分开

```yml
#设置启用的环境
spring:
  profiles:
    active: dev
---
#开发
spring:
  config:
    activate:
      on-profile: dev
server:
  port: 80
---
#生产
spring:
  profiles: pro
server:
  port: 81
---
#测试
spring:
  profiles: test
server:
  port: 82
---
```

- 打完jar包之后切换环境
  - 在cmd中运行的时候后面加 --spring.profiles.active=环境名
  - 也可以用这种方式临时改参数
  - 可以在springboot官网，外化配置中查看优先级顺序

- maven为主控制环境
  - yml配置文件加载pom文件中环境控制的信息

```xml
<build>
    <--解析占位符-->
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-resources-plugin</artifactId>
            <version>3.2.0</version>
            <configuration>
                <encoding>UTF-8</encoding>                <useDefaultDelimiters>true</useDefaultDelimiters>
            </configuration>
        </plugin>
    </plugins>
</build>
   <profiles>
        <!--开发环境-->
        <profile>
            <id>dev</id>
            <properties>
                <profile.active>dev</profile.active>
            </properties>
        </profile>
    </profiles>
```

```yml
#设置启用的环境
spring:
  profiles:
    active: ${profile.active}
```

### 配置文件分类

- 四级配置文件
  - file：config/application.yml
  - file：application.yml
  - classpath：config/application.yml
  - classpath：application.yml
- 作用
  - 1 2级用于系统打包后设置通用属性
  - 3 4级用于系统开发阶段使用



## 整合mybatis

- 加起步依赖

- 配置文件

  ```yml
  spring:
    datasource:
      driver-class-name: com.mysql.cj.jdbc.Driver
      url: jdbc:mysql://localhost:3306/ssm_db?serverTimezone=UTC
      username: root
      password: root
      type: com.alibaba.druid.pool.DruidDataSource
  ```

- 在dao上加@Mapper



# 5

# MybatisPlus

- 先在pom里导坐标
- dao中继承BaseMapper<实体类名>

## Lombok

- pom导坐标
- @Data
- 作用：为实体类提供getset方法

## 分页

- selectPage

```java
//IPage对象封装了分页操作相关的数据
//Page(第几页，每页显示几条)
IPage page  = new Page(2,3);
userDao.selectPage(page,null);
System.out.println("当前页码值："+page.getCurrent());
System.out.println("每页显示数："+page.getSize());
System.out.println("一共多少页："+page.getPages());
System.out.println("一共多少条数据："+page.getTotal());
System.out.println("数据："+page.getRecords());
```

- 创建config包mpconfig类，设置分页拦截器

```java
@Configuration
public class MpConfig {
    @Bean
    public MybatisPlusInterceptor mpInterceptor(){
        //1.定义Mp拦截器
        MybatisPlusInterceptor mpInterceptor = new MybatisPlusInterceptor();
        //2.添加具体的拦截器
        mpInterceptor.addInnerInterceptor(new PaginationInnerInterceptor());
        return mpInterceptor;
    }
}
```

- 另：

```yml
# 开启mp的日志（输出到控制台）
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
```

## 条件查询

- 减少log乱七八糟的输出

  - 配置文件建立logback.xml

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <configuration>
  </configuration>
  ```

  - 在application中加配置

  ```yml
  #spring中
  main:
    banner-mode: off
  #mybatis-plus中
  global-config:
    banner: false
  ```

- 查询方式

```java
//       方式一：按条件查询
        QueryWrapper qw = new QueryWrapper();
        qw.lt("age",18);
        List<User> userList = userDao.selectList(qw);
        System.out.println(userList);

//        方式二：lambda格式按条件查询
        QueryWrapper<User> qw = new QueryWrapper<User>();
        qw.lambda().lt(User::getAge, 10);
        List<User> userList = userDao.selectList(qw);
        System.out.println(userList);

//        方式三：lambda格式按条件查询
        LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<User>();
        lqw.lt(User::getAge, 10);
lqw.lt(User::getAge, 30).gt(User::getAge, 10);//链式编程多条件，并关系
lqw.lt(User::getAge, 10).or().gt(User::getAge, 30);//或者关系
        List<User> userList = userDao.selectList(lqw);
        System.out.println(userList);
```

- 空值判定

```java
//      先判定第一个参数是否为true，如果为true连接当前条件
        lqw.lt(null != uq.getAge2(),User::getAge, uq.getAge2());
```

## 查询投影

```java
//查询投影
LambdaQueryWrapper<User> lqw = new LambdaQueryWrapper<User>();
lqw.select(User::getId,User::getName,User::getAge);
QueryWrapper<User> lqw = new QueryWrapper<User>();
lqw.select("id","name","age","tel");
List<User> userList = userDao.selectList(lqw);
System.out.println(userList);

QueryWrapper<User> lqw = new QueryWrapper<User>();
lqw.select("count(*) as count, tel");
lqw.groupBy("tel");
List<Map<String, Object>> userList = userDao.selectMaps(lqw);
System.out.println(userList);
```



## 映射匹配兼容性

- 不对应
  - @TableField(value = "数据库中字段名")
- 编码中添加了数据库中未定义的属性
  - @TableField(exist = false)
- 不参与查询
  - @TableField(select = false)
- 表名映射
  - @TableName("tbl_user")



## DML编程控制

### id生成策略控制

- @TableId
  - 通常assignId
  - @TableId(value="id",type="")
- 全局设定：在yml配置文件中

```yml
# mp日志
mybatis-plus:
  configuration:
    log-impl: org.apache.ibatis.logging.stdout.StdOutImpl
  global-config:
    banner: false
    db-config:
      #id生成策略
      id-type: assign_id
      #表名前缀
      table-prefix: tbl_
```

### 批量删除

- .deleteBatchIds
  - 传List

### 逻辑删除

- 表中给字段（记得设置初始值）
- @TableLogic(value = "0", delval = "1")
- 或在配置文件中设置

```yml
# 逻辑删除字段名
logic-delete-field: deleted
# 逻辑删除字面值：未删除为0
logic-not-delete-value: 0
# 逻辑删除字面值：删除为1
logic-delete-value: 1
```

### 乐观锁：解决并发问题

- @Version
- 拦截器

```java
//3.添加乐观锁拦截器
mpInterceptor.addInnerInterceptor(new OptimisticLockerInnerInterceptor());
return mpInterceptor;
```

```java
User user = new User();
user.setId(3L);
user.setName("Jock666");
user.setVersion(1);
userDao.updateById(user);

//1.先通过要修改的数据id将当前数据查询出来
User user = userDao.selectById(3L);
//2.将要修改的属性逐一设置进去
user.setName("Jock888");
userDao.updateById(user);
```



## 代码生成器

- 导gengerator坐标和velocity坐标

- 建个类

```java
public class Generator {
    public static void main(String[] args) {
        AutoGenerator autoGenerator = new AutoGenerator();

        DataSourceConfig dataSource = new DataSourceConfig();
        dataSource.setDriverName("com.mysql.cj.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql://localhost:3306/mybatisplus_db?serverTimezone=UTC");
        dataSource.setUsername("root");
        dataSource.setPassword("root");
        autoGenerator.setDataSource(dataSource);

        autoGenerator.execute();
    }
}
```

- 再建个类

```java
public class CodeGenerator {
    public static void main(String[] args) {
        //1.获取代码生成器的对象
        AutoGenerator autoGenerator = new AutoGenerator();

        //设置数据库相关配置
        DataSourceConfig dataSource = new DataSourceConfig();
        dataSource.setDriverName("com.mysql.cj.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql://localhost:3306/mybatisplus_db?serverTimezone=UTC");
        dataSource.setUsername("root");
        dataSource.setPassword("root");
        autoGenerator.setDataSource(dataSource);

        //设置全局配置
        GlobalConfig globalConfig = new GlobalConfig();
        globalConfig.setOutputDir(System.getProperty("user.dir")+"/mybatisplus_04_generator/src/main/java");    //设置代码生成位置
        globalConfig.setOpen(false);    //设置生成完毕后是否打开生成代码所在的目录
        globalConfig.setAuthor("黑马程序员");    //设置作者
        globalConfig.setFileOverride(true);     //设置是否覆盖原始生成的文件
        globalConfig.setMapperName("%sDao");    //设置数据层接口名，%s为占位符，指代模块名称
        globalConfig.setIdType(IdType.ASSIGN_ID);   //设置Id生成策略
        autoGenerator.setGlobalConfig(globalConfig);

        //设置包名相关配置
        PackageConfig packageInfo = new PackageConfig();
        packageInfo.setParent("com.aaa");   //设置生成的包名，与代码所在位置不冲突，二者叠加组成完整路径
        packageInfo.setEntity("domain");    //设置实体类包名
        packageInfo.setMapper("dao");   //设置数据层包名
        autoGenerator.setPackageInfo(packageInfo);

        //策略设置
        StrategyConfig strategyConfig = new StrategyConfig();
        strategyConfig.setInclude("tbl_user");  //设置当前参与生成的表名，参数为可变参数
        strategyConfig.setTablePrefix("tbl_");  //设置数据库表的前缀名称，模块名 = 数据库表名 - 前缀名  例如： User = tbl_user - tbl_
        strategyConfig.setRestControllerStyle(true);    //设置是否启用Rest风格
        strategyConfig.setVersionFieldName("version");  //设置乐观锁字段名
        strategyConfig.setLogicDeleteFieldName("deleted");  //设置逻辑删除字段名
        strategyConfig.setEntityLombokModel(true);  //设置是否启用lombok
        autoGenerator.setStrategy(strategyConfig);
        //2.执行生成操作
        autoGenerator.execute();
    }
}
```
