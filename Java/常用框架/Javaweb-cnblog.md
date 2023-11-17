# Javaweb后端

[TOC]

# Javaweb前端

## 一、基本概念

- 静态web：html，css
  - 缺点：无法动态更新；无法和数据库交互
  - 伪动态：轮播图、点击特效（JavaScript）
- 动态web：Serlet/JSP，ASP，PHP



## 二、Web服务器

**2.1 技术讲解**

- ASP
  - 在HTML中嵌套VB的脚本，ASP+COM
  - 页面混乱，维护成本高
- php
  - 无法承载大访问量
- JSP/Serlet
  - B/S架构，基于Java
  - 可以承载三高的影响

**2.2 web服务器**

- IIS
- Tomcat



## 三、HTML CSS

##### 3.1 官方文档

w3school.com.cn

**3.2 常用标签**

- img：src 图片资源路径；width，height（单位：px 像素；% 相对于父元素的百分比）
  - 绝对路径：绝对磁盘路径；绝对网络路径。
  - 相对路径（推荐）：./ 当前目录（可以省略）；../ 上一级目录。
- \<h1>~\<h6> 标题
- \<a href = "..." target = "..."\>央视网<\a>
  - href 制定资源访问的url
  - target：\_self 在当前页面打开；\_blank 在空白页面打开。
- <video\> 视频
  - src：url
  - controls :播放控件
- <audio\> 音频
- <p\> 段落标签
- <br\> 换行
- <b\>/<strong\> 文本加粗
- 表格标签
  - <table\> 表格整体
  - <tr\> 表格的行
  - <td\> 表格的单元格，如果是表头可以替换成<th\>
- <from\> 表单标签：action 向何处（url）提交数据；method 发送表单数据的方式（GET、POST）
  - <input\> 表单项：type（text、password、radio、checkbox...），name，value
  - <select\> 下拉列表
  - <textarea\> 文本域

**3.3** **CSS的引用方式**

![image-20230709013202763](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231016184636150-1652175416.png)

- 颜色的表示形式
  - 关键字；
  - rgb表示法：红绿蓝三原色，取值0~255；
  - 十六进制表示法：#开头，十六进制表示。
- 识色器插件：live color picker
- CSS选择器
  - 元素选择器：使用一个没有语义的标签 \<span>
  - id选择器
  - 类选择器

- text-indent：首行缩进
  - 单位：px
- line-hight：行高
- text-align：文本对齐
  - center
  - left
  - right
- **页面布局**
  - 盒子模型：content, padding, border, margin；上右下左。
  - 布局标签：<div\> 独占一行，可以设置宽高；<span\> 一行可以显示多个，不可以设置宽高。



## 四、JavaScript

**4.1 引入方式**

- 内部脚本：使用<script\></script\>标签，一般放置在<body\>元素的底部
- 外部脚本：写在外部一个.js文件中，<script src = ></script\>，注意不能自闭和

**4.2 语法**

- 行末分号可不写

- 输出语句

  - window.alert() 写入警告框。
  - document.write() 写入html输出。
  - console.log() 写入浏览器控制台。

- 声明变量

  - var 可以存放不同类型的值；全局变量；可重复声明。
  - let 只在改代码块生效，不可重复声明。
  - const 常量，不可被改变。

- 数据类型

  - typeof

  - 原始类型：number、string、boolean、null、undefined

- 运算符

  - == 会进行类型转换，=== 全等运算符 不会进行类型转换。
  - parseInt 将其他类型转化为数字，如果字面值不是数字则转为NAN。

**4.3 函数**

- 通过关键字function进行定义

**4.4 对象**

- **Array** 长度可变，类型可变

  - foreach() 遍历有值的元素；箭头函数 =>
  - push() 添加元素到数组的尾部
  - splice() 删除元素

- **String**

  - charAT() 返回指定位置的字符
  - indexOf() 检索字符串
  - trim() 去除字符串两边的空格
  - subtring() 提取字符串中两个指定的索引号之间的字符

- **JSON**：通过JavaScript对象标记法书写的**文本**

  - 自定义对象

    var 对象名 = {
    	属性名1: 属性值1;
    	属性名2: 属性值2;
    	函数名称: function(形参列表){};
    }

  - JSON格式化工具

  - parse()：将JSON字符串转化为JS对象

  - stringify()：JS对象转化为JSON字符串

**4.5 BOM（对象）**

-  **window 浏览器窗口对象**
   - 属性：History，Location，navigator
   - 方法：alert()，confirm()，setInterval()，setTimeout()
-  navigator 浏览器对象
   - href 设置或返回完整的url
-  Screen 屏幕对象
-  History 历史记录对象
-  Location 地址栏对象

**4.6 Dom**

- document 整个文档对象
  element 元素对象
  Attribute 属性对象
  text 元素对象
  comment 注释对象
- Core Dom 所有文档的标准模型
  XML Dom
  HTML Dom
  image：<img\>
  Button：<input type='button'\>
- 获取元素对象：
  - id属性值 var h1 = document.getElementById('h1')
  - 标签名 getElementsTagName
  - name属性值 getElementsName
  - class属性值 getElementsClassName

**4.7 js事件**

- 事件绑定

  ![img](file:///C:\Users\31067\Documents\Tencent Files\3106799954\Image\C2C\%H__O~Z[940GJ3V8]~K9%I3.png)



## 五、Vue

**5.1 概述**

- 官网：https://v2.cn.vuejs.org

**5.2 常用指令**

- v- vind
- v- model
- 通过以上指令绑定的变量必须在数据模型中声明
- v-on：html事件绑定



## 六、Ajax

**6.1 概述**

- 给服务器端发送请求并获取服务器相应的数据
- 异步交互

**6.2 Axios**

- 官网：www.axios-http.cn



## 七、前端工程化

**7.1** **YApi**

yapi.smart-xwork.cn

**7.2 Vue-cil**

- vue 脚手架工具



## 八、Element





# JavaWeb后端

# Maven

**1.1 Apache软件基金会**

www.apache.org/index.html#projects-list

**1.2 ** **依赖范围**

![image-20230713200529539](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231016184320584-1739513643.png)



# HTTP

**2.1 概述**

规定了数据传输的规则

**2.2 特点**

- 基于TCP
- 请求-响应模型
- 无状态 - > 速度快，但多次请求间不能共享

**2.3 请求协议**

- 请求行
- 请求头

![image-20230714002634678](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231016184320069-1283172251.png)

- 请求体：POST请求，存放请求参数

**2.4 （补充）GET和POST区别**

2.4.1 数据传输方式：

- GET：GET请求将数据附加在URL的末尾，作为查询参数传输。这些参数会显示在浏览器的地址栏中，因此对于传输**敏感信息不太安全**。GET请求使用URL长度限制（通常为2048个字符），因此传输的**数据量有限**。
- POST：POST请求将数据包含在请求的消息体中，而不会显示在URL中。由于数据不会直接显示在URL上，POST请求对于传输敏感信息更加**安全**。POST请求没有URL长度限制，可以传输更**大量**的数据。

2.4.2 数据语义：

- GET：GET请求用于获取资源，它是一种幂等操作，多次发送相同的GET请求不会对服务器资源产生副作用。GET请求应该只用于读取数据，而不应该用于对服务器进行修改。
- POST：POST请求用于向服务器提交数据，可能会对服务器资源进行修改或产生副作用。POST请求是非幂等的，多次发送相同的POST请求可能会对服务器产生不同的结果。

2.4.3 缓存：

- GET：GET请求可以被浏览器缓存，可以通过缓存机制减少对服务器的请求，提高性能。但是，缓存的数据可能会被其他人查看或访问。
- POST：POST请求默认情况下不会被缓存，每次发送POST请求都会向服务器请求最新的数据。

总结来说，**GET适合用于获取资源，数据量较小，且不涉及敏感信息的传输**。而**POST适合用于向服务器提交数据，数据量较大，或者涉及敏感信息的传输**。

**2.5 响应协议**

- 响应行

![image-20230714004248703](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231016184319528-1077693071.png)

![image-20230714004518241](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231016184319064-142973286.png)

- 响应头

![image-20230714005518729](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231016184318468-564335537.png)

- 响应体，存放响应数据



# 请求响应

## 请求

- 简单参数
  - 定义方法形参，请求参数与形参变量名一致
  - 不一致可通过@RequestParam手动映射

```java
//post http://localhost:8080/method12?id=1&name=小小  
@PostMapping("/method12")
    public ApiResponse method12(@RequestParam(value = "id",required = true) int id, @RequestParam(value = "name",required = false)String name){
        String s = id + "" + name;
        System.out.println(s);
        return ApiResponse.ok().data(s);
    }
```

- 数组集合参数

  - 数组：请求参数名与数组名一致，直接封装
  - 集合：请求参数名与集合名一致，@RequestParam绑定关系

- 日期参数

  - @DataTimeFormat(pattern = "yyyy-MM-dd")

- JSON参数：@RequestBody

  ```java
  @PostMapping("/method4")
      public ApiResponse method4(@RequestBody Dog dog){
          System.out.println(dog.toString());
          return ApiResponse.ok().data(dog);
      }
  ```

- 路径参数

  - @PathVariable

- @RestController = @Controller（用于标记一个类为处理HTTP请求的控制器）  + @ResponseBody（用于标记方法返回值将直接作为响应体返回给客户端；返回数据格式，如JSON、XML等）

## （补充）xml文件

- xml解析代码实现

  ```java
  //创建解析器
  SAXReader saxReader = new SAXReader();
  //获取Document 对象
  Document document = saxReader.read(new FileInputStream("itheima-xml\\students.xml"));
  //获取根节点对象
  Element rootElement = document.getRootElement();
  //从根节点来查找其他的节点元素
  List<Element>elements = rootElement.elements("student");
  
  for (Element studentElement: elements) {
      //获取属性
      Attribute attribute = studentElement.attribute("id");
      // 获取id属性的值
      String id = attribute.getValue();
      // 获取name元素
      Element nameElements = studentElement.element("name");
      //获取name元素对应的内容
      String name = nameElements.getText();
      //获取age元素
      Element ageElements = studentElement.element("age");
      //获取age元素对应的内容
      String age = ageElements.getText();
  }
  ```

- 动态地获取文件路径

  ```java
  String file = this.getClass().getClassLoader().getResource("emp.xml").getFile();
  ```



# 分层解耦

## 三层架构

由上到下依次是controller（接受页面传过来的参数传给service处理）、service（对一个或多个dao封装成服务）、dao（mapper）层（对应数据库增删改查）。

详细参考：https://blog.csdn.net/qq_26866883/article/details/116886614

## IOC/DI入门

1. Service层及Dao的实现类，交给IOC容器管理，成为IOC容器中的bean - > 在类前加@Component
2. 为Controller及Service注入运行时依赖的对象 - > 在变量前加@Autowaired（运行时，IOC容器会提供该类型的bean对象，并赋值给该对象）
3. 作用：层与层之间解耦



## IoC 控制反转

- 注解：
  - @Component 不属于以下三类时用
  - @Controller 标注在控制器类型上 
  - @Service 标注在业务类型上
  - @Repository 标注在数据访问类上（与mybatis整合，用得少）

- @ComponentScan({"com.example","com.itheima"}) 扫描指定包
  - SpringBootApplication默认扫描**当前包及其子包**

## DI 依赖注入

- @Autowaired默认按照类型进行，如果存在多个相同类型的bean
  - @Primary 优先级，让当前bean生效
  - @Qualifier("xx")：xx为bean的名字（默认为类名，首字母小写）
  - @Resource：去掉@Autowaired，改为@Resource(name = "xx")，xx为bean的名字





# MySQL

## 事务

```sql
开启事务：start transaction;/begin;
提交事务：commit;
（若部分报错，则）回滚事务：rollback;
```

- 四大特性：原子性、一致性、隔离性、持久性

## 索引

- 结构：B+ 树，所有的数据都存储在叶子结点，叶子结点间有双向链表

- 语法

  ```sql
  create [unique] index 索引名 on 表名(字段名,...);
  show index from 表名; 
  drop index 索引名 on 表名;
  ```


## 分页查询

- 分页查询语法

  ```mysql
  -- 参数1：起始索引（从0开始）= （页码-1）*每页展示的记录数
  -- 参数2：查询返回记录数
  select * from emp limit 0,5
  ```

  







# MyBatis

## 快速入门

1. 引入MyBatis依赖

2. 配置MyBatis数据库连接信息

   - 驱动类名称；url；用户名；密码

3. 创建User实体类

4. 创建mapper接口

   - **@Mapper** 在运行时会自动生成该接口的实现类对象，并且将该对象交给IOC容器管理
   - **@Select**("sql语句") 

   ```java
   @Mapper
   public interface UserMapper{
       @Select("select * from user")
       public List<User> list();
   }
   ```



## 数据库连接池

## Lombok

- @Data
- @NoArgConstructor 无参构造
- @AllArgsConstructor 全参构造

## 增删改查

### 删除

- ```java
  // #{id} 被？替代，采用预编译sql
  @Delete("delete from emp where id = #{id};")
  ```

- ```java
  // ${id} 直接拼接，有sql注入的风险
  @Delete("delete from emp where id = ${id};")
  ```

### 新增

- 需要传入太多数据时 可以传入对象，注意#{}中的内容要和对象里的字段（驼峰命名）一致，而不是和表中的字段（下划线命名）一致

- 主键返回：在insert注解上方再加一个注解

  ```java
  @Options(keyProperty = "id", userGeneratedKeys = true)
  ```

### 查询

- 如果 实体类属性名 和 数据库表查询返回的字段名 一致，mybatis会自动封装；不一致则不能。

  - 方法一：给字段起别名

    ```java
    @Select("select dept_id deptId, create_time createTime from emp where id = #{id}")
    ```

  - 方法二：采用@Result，Results注解

    ```java
    @Results({
        //前者为表中的字段名，后者为类中的属性名
        @Result(colum = " ",property = " ")
    })
    ```

  - **方法三**：（配置文件）开启mybatis的驼峰命名自动映射开关

  ```java
  #开启mybatis驼峰命名自动映射开关
  mybatis.configuration.map-underscore-to-camel-case=true
  ```

  

### 条件查询

- '%张%' 应该用 '%${name}%'而不是'%#{name}%'，因为 ? 不能在 ’ ‘ 内
- 问题：不安全，sql注入等
- 解决：concat字符串拼接函数 写成concat('%', #{name}, '%')



## xml映射文件

- xml映射文件的名称与mapper接口名称一致，并且将xml映射文件和mapper接口防治在相同包下（相同包名）
  - 多级目录不能用.分隔，要用/分隔
- xml文件中namespace属性与mapper接口全限定名一致
- xml映射文件中sql语句的id与mapper接口中的方法名一致，并保持返回类型一致（resultType是单条记录所封装的类型）

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.itheima.mapper.EmpMapper">
    <select id="list" resultType="com.itheima.pojo.Emp">
        select * from emp where name like concat ('%',#{name},'%') and gender = #{gender} and entrydate between #{begin} and #{end} order by update_time desc
    </select>
</mapper>
```



## 动态sql

- <if\>标签：条件判断，如果成立则拼接
- <where\>标签：动态地去掉多余的and，or等，保证sql语句的准确性

```xml
//动态查询
<select id="list" resultType="com.itheima.pojo.Emp">
        select *
        from emp
        <where>
          <if test="name!=null">
              name like concat('%', #{name}, '%')
          </if>
          <if test="gender!=null">
              and gender = #{gender}
          </if>
          <if test="begin != null and end != null">
              and entrydate between #{begin} and #{end}
          </if>
        </where>
        order by update_time desc
    </select>
```

- 批量删除

  ```java
   void deleteByIds(List<Integer> ids);
  ```

  ```xml
  <delete id="deleteByIds">
          delete from emp where id in
              <foreach collection="ids" item="id" separator="," open="(" close=")">
                  #{id}
              </foreach>
      </delete>
  ```

- sql & include：提高代码的复用性

  ```xml
  id自己取
  <sql id = "aaa">
      里面写可复用的代码
  </sql>
  需要这段代码的地方直接用以下代替
  <include refid = "aaa"/>
  ```



# 案例 部门管理

- Controller层优化：将类中的公共路径用**@RequestMapping**("depts") 注解提到类前
- @RequestParam注解：属性defaultValue可以来设置参数的默认值

## 分页查询

- 请求参数：页码，每页展示记录数

- 响应结果：总记录数、结果列表

- **PageHelper插件**

  ```java
  @Select("select * from emp")
  List<Emp> list();
  ```

  ```java
  @Override
  public PageBean page(Integer page, Integer pageSize){
  	PageHelper.startPage(page,pageSize);
      
      List<Emp> empList = empMapper.list();
      Page<Emp> p = (Page<Emp>) empList;
      
      PageBean pageBean = new PageBean(p.getTotal(),p.getResult());
      return pageBean;
  }
  ```


- 如果需要使用动态sql，只需将@Select("select * from emp")删掉然后在配置文件里根据要求写即可




## 文件上传

- MutipartFile ：注意该表单项的名称需要与方法形参的名称保持一致

  - 若不一致，可用@RequestParam("image") 括号中表示的是表单项中的名称

- 本地存储

  ```java
   MultipartFile image;
  //获取原始文件名
          String originalFilename = image.getOriginalFilename();
  //取后缀
          int index = originalFilename.lastIndexOf(".");
          String extname = originalFilename.substring(index);
  //构造唯一的文件名 --uuid(通用唯一识别码)
          String newFileName = UUID.randomUUID().toString()+ extname;
  //存储在磁盘目录中
          image.transferTo(new File("D:\\images\\"+ newFilename));
  ```

- 上传大文件：在properties中配置

  ```properties
  # 配置单个文件最大上传大小
  spring.servlet.multipart.max-file-size=10MB
  #配置单个请求最大上传大小（一次请求可以上传多个文件)
  spring.servlet.multipart.max-request-size=100MB
  ```

  

## 阿里云OSS

- 添加依赖

  ```xml
  <dependency>
      <groupId>com.aliyun.oss</groupId>
      <artifactId>aliyun-sdk-oss</artifactId>
      <version>3.15.1</version>
  </dependency>
  ```

- 详见官方文档：[如何安装OSS Java SDK_对象存储 OSS-阿里云帮助中心 (aliyun.com)](https://help.aliyun.com/zh/oss/developer-reference/java-installation?spm=a2c4g.11186623.0.0.16dc3ba3ze9FcS)



# 配置文件

### 参数配置化

- 用配置文件代替硬编码
- @Value("${}") 赋值

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
  #对象/Map集合
  user:
  	name: zhangsan
  	age: 18
  
  #数组/List/Set集合
  hobby:
   - java
   - game
   - sport
  ```

- xml 臃肿
  - 标签

- @ConfigurationProperties(prefix = "前缀") 可以批量将外部的属性配置注入到bean对象的属性中

  - 添加依赖（可选）

  ```xml
      <dependency>
              <groupId>org.springframework.boot</groupId>
              <artifactId>spring-boot-configuration-processor</artifactId>
          </dependency>
  ```



# 接上案例

## 登录校验 

### 会话技术

- 会话：一次会话中可以包含多次请求和响应
- 会话跟踪：维护浏览器状态，服务器识别是否来自同一浏览器，以便在同一次会话的多次请求间共享数据
  - 客户端会话跟踪Cookie：Http协议中支持的技术；移动端无法使用Cookie；不安全，用户可以禁用；Cookie不能跨域；
  - 服务器端会话跟踪Session：存储在服务器端，安全；服务器集群环境下无法使用；Cookie的缺点；
  - 令牌技术（目前主流）：支持PC端、移动端；解决集群环境下的认证问题；减轻服务器端存储的压力

### JWT令牌

- 官网：https://jwt.io

- 依赖

  ```xml
   <dependency>
              <groupId>io.jsonwebtoken</groupId>
              <artifactId>jjwt</artifactId>
              <version>0.9.1</version>
          </dependency>
  ```

- 组成：

  - Header 头，记录令牌类型，签名算法
  - Payload 有效载荷，携带一些自定义信息
  - Signature 签名，将header、payload、制定秘钥通过指定签名算法计算而来

- 生成jwt令牌

  ```java
  public void testGenJwt(){
          Map<String, Object> claims = new HashMap<>();
          claims.put("id",1);
          claims.put("name","tom");
          String jwt = Jwts.builder()
                  .signWith(SignatureAlgorithm.HS256, "xiaoyang")//签名算法
                  .setClaims(claims)//自定义内容（载荷）
                  .setExpiration(new Date(System.currentTimeMillis() + 3600 * 1000))//设置有效期为一个小时
                  .compact();
          System.out.println(jwt);
      }
  ```

- 将生成的令牌放入官网（也有在线解码工具）可以自动解析令牌携带的信息

- 解析（一般会创建一个新的工具类，存放生成令牌和解析令牌的方法）

  ```java
   @Test
      public void testParseJwt(){
          Claims claims = Jwts.parser()
                  .setSigningKey("xiaoyang")           		.parseClaimsJws("eyJhbGciOiJIUzI1NiJ9.eyJuYW1lIjoidG9tIiwiaWQiOjEsImV4cCI6MTY5MzE0NDQ0OH0.yvP02RKkPftX8y7N4Ks9izQoBafelA0-z9xwT6DWkLc")
                  .getBody();
          System.out.println(claims);
      }
  ```

## 拦截

### 过滤器Filter

- 拦截对资源的请求，实现特殊功能
- 在启动类加 @ServletComponentScan ，开启对Servlet组件的支持
- 标准接口：Filter（import javax.servlet.*）

### 过滤器链

- 多个过滤器拦截
- 优先级按字母排序



## 登录校验过滤器

- 字符串转换

  ```xml
    <dependency>
              <groupId>com.alibaba</groupId>
              <artifactId>fastjson</artifactId>
              <version></version>
          </dependency>
  ```

- 登录

  ```java
  @Slf4j
  @WebFilter(urlPatterns = "/*")
  public class LoginCheckFilter implements Filter {
      @Override
      public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
          HttpServletRequest req = (HttpServletRequest) servletRequest;
          HttpServletResponse resp = (HttpServletResponse) servletResponse;
  
          String url = req.getRequestURL().toString();
          log.info("请求的url：{}", url);
  
          if (url.contains("login")) {
              log.info("登录操作 放行");
              filterChain.doFilter(servletRequest, servletResponse);
              return;
          }
          String jwt = req.getHeader("token");
  
          if (!StringUtils.hasLength(jwt)) {
              log.info("请求头token为空");
  
              Result error = Result.error("NOT_LOGIN");
              String notLogin = JSONObject.toJSONString(error);
              resp.getWriter().write(notLogin);
              return;
          }
          try {
              JwtUtils.parseJWT(jwt);
          } catch (Exception e) {
              e.printStackTrace();
              log.info("解析失败");
              Result error = Result.error("NOT_LOGIN");
              String notLogin = JSONObject.toJSONString(error);
              resp.getWriter().write(notLogin);
              return;
          }
          log.info("令牌合法，放行");
          filterChain.doFilter(servletRequest,servletResponse);
  
      }
  }
  ```



## 拦截（二）

### 拦截器Interceptor

- Spring框架中提供的

- 标准接口 

- 定义拦截器

  ```java
  @Component
  public class LoginCheckInterceptor implements HandlerInterceptor {
      //目标资源方法运行前运行
      @Override
      public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
          return HandlerInterceptor.super.preHandle(request, response, handler);
      }
      //目标资源方法运行后运行
      @Override
      public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
          HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
      }
      //视图方法渲染完毕后运行
      @Override
      public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
          HandlerInterceptor.super.afterCompletion(request, response, handler, ex);
      }
  }
  ```

- 注册配置拦截器

  ```java
  @Configuration
  public class WebConfig implements WebMvcConfigurer {
      @Autowired
      private LoginCheckInterceptor loginCheckInterceptor;
      @Override
      public void addInterceptors(InterceptorRegistry registry) {
  //.addPathPatterns("")表示要拦截的资源
  //.excludePathPatterns()表示不拦截某些资源       
          registry.addInterceptor(loginCheckInterceptor).addPathPatterns("/**");
      }
  }
  ```

- 常用拦截路径

  - /* 一级路径
  - /** 任意级路径

# 异常处理

- 全局异常处理器

```java
//该注解中包括@ResponseBody，会将返回值转为json格式 
@RestControllerAdvice
public class GlobalExceptionHander{
    @ExceptionHandler(Exception.class)//捕获所有异常
    public Result ex(Exception ex){
        ex.printStackTrace();//输出异常的堆栈信息
        return Result.error("对不起，请联系管理员");
    }
}
```



# 事务管理

- @Transactional

  - 位置：service层的方法上、类上、接口上
  - 默认情况下，只有RuntimeException才回滚异常

- rollbackFor：控制出现何种异常类型，回滚事务

  - eg. @Transactional(rollbackFor = Exception.class)

- propagation：控制事务的传播行为，默认为REQUIRED（需要事务，有则加入无则新建）

  - REQUIRES_NEW：每次都开启一个新的事物

- 开启事务管理日志

  ```properties
  #spring事务管理日志
  logging:
    level:
      org.springframework.jdbc.support.JdbcTransactionManager: debug
  ```



# AOP

- 面相特定方法编程

- 运行顺序：类名

  - @Order(数字) 数字越小越先执行

  ```java
  @Slf4j
  @Component
  @Aspect
  public class timeAspect {
  
      @Around("execution(* com.itheima.service.*.*(..))")//切入点表达式
      public Object recordTime(ProceedingJoinPoint joinPoint) throws Throwable {
  
          long begin = System.currentTimeMillis();
          Object result = joinPoint.proceed();
          long end = System.currentTimeMillis();
  
          log.info(joinPoint.getSignature()+"方法执行耗时:{}ms",end-begin);
      }
  }
  ```

## 相关概念

- 连接点：JoinPoint 可以被AOP控制的方法
- 通知：Advice 被提取出的重复逻辑
- 切入点：PointCut 匹配连接点的条件
- 目标对象：Target 通知所应用的对象

## 通知类型

- @Around 环绕
  - 只有它需要自己调用 Object result = joinPoint.proceed() 来让原始方法执行
  - 返回值必须指定为Object，来接收原始方法的返回值
- @Before 前置通知
- @After 后置通知
- @AfterReturning 返回后通知
- @AfterThrowing 异常后通知

## 切入点表达式

- 抽取（复用）

```java
@Pointcut("execution(* com.itheima.service.*.*(..))")
private void pt(){};
```

- execution

  - execution(访问修饰符? 返回值 包名.类名.? 方法名（方法参数) throws 异常?)    其中带?的可以省略
  - 通配符：*  ..
  - 尽量基于接口描述，增强拓展性

- @annotation(注解全类名)

  - 新建一个注解

  ```java
  @Retention(RetentionPolicy.RUNTIME)
  @Target(ElementType.METHOD)
  public @interface MyLog {
  }
  ```

## 连接点

- JoinPoint 获取连接点信息
  - @Around 通知只能用 ProcrrdingJoinPoint
  - 获取操作类型
  
  ```java
  @Before("autoFillPt()")
  public void autoFill(JoinPoint jp)throws Throwable{
      log.info("开始进行公共字段的自动填充...");
  
      MethodSignature signature =(MethodSignature) jp.getSignature();
      AutoFill annotation = signature.getMethod().getAnnotation(AutoFill.class);
      OperationType operationType = annotation.value();
  }
  ```

## 实践：记录增删改日志



# SpringBoot

## 配置优先级

- 命令行参数 > Java系统属性  > properties > yml > yaml

## Bean的管理

### bean的获取

```java
@Autowired
private ApplicationContext applicationContext;
```

- 根据名称（默认为首字母小写）
- 根据bean的类型
- 根据bean的名称及类型

### bean的作用域

- singleton 默认
- prototype 每次创建新的实例
- @Scope() 
- @Lazy() 延迟（到第一次使用的时候）初始化

### 第三方Bean的管理

- @Bean 将方法的**返回值**交给IOC容器管理

  ```java
  @Bean
  public SAXReader saxReader(){
      return new SAXReader();
  }
  ```

- 一般通过@Configuration 声明一个配置类，几种分类配置

- 在第三方bean中实现注入：直接写在方法形参中即可

## SpringBoot原理

### 起步依赖

### 自动配置

- @ComponentScan({"com.example","com.itheima"}) 扫描指定包
- @Import({mySelector.class}) 导入
- @Enablexxxx注解 封装Import注解

- @ConditionalOnClass 判断环境中是否有对应的字节码文件
- @ConditionalOnMissingBean 判断有没有对应的bean
- @ConditionalOnProperty 判断是否有对应的属性和值

### 自定义starter

# 总结

![image-20230831234117231](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231016184318026-440425516.png)



# Maven高级

## 分模块设计 

- eg. 把实体类、工具类分别单独用模块存放，需要的时候引入依赖


## 继承与聚合

### 继承关系

- 将公共依赖写在父工程中，子工程可以直接继承父工程的配置信息

- 只能单继承

  ![image-20230901000723500](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231016184317392-2104858281.png)

### 版本锁定

- 在父工程中用<dependencyManagement\>统一管理版本，子工程无须再指定版本

- 自定义属性

  ![image-20230901001810112](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231016184316598-1394456257.png)

### 聚合

- 原：先install再package

- 聚合工程：一个不具有业务功能的工程（一般为父工程）

  ![image-20230901002318526](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231016184315913-1901883077.png)

  

# 私服

- 架设在局域网内的仓库服务
- 仓库
  - release 发行版本
  - snapshot 快照版本
  - central 从中央仓库下载的
- 配置过程详见文档

[maven私服配置（from黑马案例）_大鱼吃大鱼的博客-CSDN博客](https://blog.csdn.net/m0_74288857/article/details/131196721?ops_request_misc=&request_id=&biz_id=102&utm_term=黑马私服配置说明&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-131196721.142^v93^chatgptT3_2&spm=1018.2226.3001.4187)













































