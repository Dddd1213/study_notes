# OJ

[TOC]

# 设计思路

## 基本功能

- 题目：题目背景，题目描述，输入格式，输出格式，输入输出样例
- 限制：时间限制，内存限制
- 提交：语言选择
- 检验：不能随便引入包，不能采用奇怪的方式暴力破解
- 判题：异步进行
- 结果：提交信息，运行结果（是否AC），运行信息

## 常用概念

- 测评方式
  - 普通测评：把示例输入放入用户提交代码中，把输出和正确输出比较
  - 特殊测评（SPJ）：写一个特殊的判断程序，程序接受标准输入，标准输出，用户的结果，然后进行比对

## 核心

- 权限校验
- 代码沙箱
  - 隔离的安全环境，不让用户代码影响系统运行
- 资源分配：限制用户程序的占用资源
- 判题规则
- 任务调度：消息队列

## 流程图

![image-20231208001131776](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231208001131776.png)



## 模块

1. 题目模块
   - 创建题目（管理）
   - 删除题目（管理）
   - 修改题目（管理）
   - 搜索题目
   - 在线做题
   - 提交题目代码
2. 用户模块
   - 注册
   - 登录
     - 验证码
3. 判题模块
   - 提交判题（结果是否正确）
   - 错误处理（内存溢出，安全性，超时）
   - 代码沙箱
   - 开放接口（提供一个独立的新服务）

## 现成的

- judge0：很完善，提供了很多api



## 技术选型

- SpringBoot + Mybatis(Plus)
- JWT
  - 登录校验
- RabbitMQ
  - 异步化



# 02

# 初始化 后端 启动！！

## 表设计

### 用户表（User）

```sql
create table user
(
    id           bigint auto_increment
        primary key,
    userAccount  varchar(256)                           not null comment '账号',
    userPassword varchar(512)                           not null comment '密码',
    userName     varchar(256)                           null comment '用户昵称',
    userAvatar   varchar(1024)                          null comment '用户头像',
    userRole     varchar(256) default 'user'            not null comment '用户角色：user/admin',
    createTime   datetime     default CURRENT_TIMESTAMP not null comment '创建时间',
    updateTime   datetime     default CURRENT_TIMESTAMP not null,
    isDelete     tinyint      default 0                 null comment '是否被删除'
);
```

### 题目表（question）

- tags varchar(512)
  - json数组字符串
- judgeConfig
  - json对象
  - 存时间（timeLimit）内存（memoryLimit）限制
  - 便于拓展
- judgeCase
  - 输入（input）输出（output）用例，成组存放

```sql
create table question
(
    id          bigint                             not null
        primary key,
    title       varchar(512)                       not null comment '标题',
    content     text                               null comment '内容（背景、题目描述等）',
    tags        varchar(512)                       null comment '标签',
    answer      text                               null comment '答案',
    submitNum   int      default 0                 not null comment '提交总数',
    acceptedNum int      default 0                 not null,
    judgeConfig text                               null comment '判题配置（json数组）（timeLimit，memoryLimit）
',
    judgeCase   text                               null comment '判题用例（json数组）（input，output）',
    userId      bigint                             null comment '题目创建人',
    createTime  datetime default CURRENT_TIMESTAMP not null,
    updateTime  datetime default CURRENT_TIMESTAMP not null,
    isDelete    tinyint  default 0                 not null,
    constraint table_name_id_uindex
        unique (id)
);
```

### 题目提交表

- judgeInfo
  - message，time，memory
  - 判题信息枚举值：Accepted，Wrong Answer，Compile Error，Memory Limit Exceeded，Time Limit Exceeded，Presentation Error，Output Limit Exceeded，Waiting，Dangerous Operation，Runtime Error

```sql
create table question_submit
(
    id         bigint                             not null primary key,
    questionId bigint                             not null comment '题目id',
    userId     bigint                             not null comment '用户id',
    `language` varchar(128)                       not null comment '编程语言',
    `code`     text                               not null comment '提交代码',
    `status`   int                                not null comment '判题状态 0-待判题，1-判题中，2-成功，3-失败',
    judgeInfo  text                               null comment '判题信息（json）',
    createTime datetime default CURRENT_TIMESTAMP not null,
    updateTime datetime default CURRENT_TIMESTAMP not null,
    isDelete   tinyint  default 0                 not null,
    constraint table_name_id_uindex
        unique (id),
    index idx_userId (userId),
    index idx_questionId (questionId)
)comment '题目提交表';
```





## 数据库相关

- 配置mysql
- 配置mybatis（plus）
- updateTime自动填充



## 用户相关

- 登录，注册，登出，获取当前登录用户信息



## 增删改查

- TODO：edit方法，add校验非空字段是否为空
- 为了获取数据更方便，这里把用json格式String类型存在数据库中的数据单独定义了新的封装类，并在VO中写了VO和Entity之间的转换（主要是拆开json）

### 查

- 一个方法中根据任意个字段查询用queryWrapper也能实现

- 分页查询查数据库查出来是Question类，怎么把分页的内容转成QuestionVO？

  - 用Stream流的map做一个转换！

  - ```java
    //setRecords()可以把处理后的list集合用放入分页中！
     questionVOPage.setRecords(questionVOList);
    ```

- TODO：submit的分页查没做呢还



# 03

# 判题机模块

- 判题服务：
  - 调用代码沙箱，发送题目代码，**一组**输入用例
    - 一组：减少网络传输次数和编译次数
  - 接收沙箱传过来的**一组**程序输出
  - 判题
- 代码沙箱：
  - 接收代码，输入用例
  - 返回程序输出
  - 主要作用是起一个运行和环境隔离。
- 服务解耦：API交互

## 代码沙箱开发

- 定义代码沙箱接口

- IOC容器管理

  - 一个接口对应多个实现类怎么用注入（**bean的默认名是类名首字母小写**）

    ```java
        @Resource(name = "remoteCodeSandbox")
    //    @Resource(type = RemoteCodeSandbox.class)
        CodeSandbox codeSandbox;
    ```

- 不用IOC，这里new沙箱（鱼皮这么写的，可以把工厂模式加进去..）
- 使用工厂模式，根据用户传入的字符串参数，生成对应的代码沙箱实现类（为什么不用IOC/DI注入）

### 踩坑

- switch…case语句的case后面的值**必须为常量**，因为switch在编译的时候需要确保case里面的值必须不能相同。如果这么写可能case会出现重复，编译器不认！

![image-20231209173806882](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231209173806882.png)

- 解决：先通过用户传来的值，找到对应的枚举的内容；然后使用枚举对象作为switch对象，case 枚举对象

  ```java
  /**
   * 根据用户传来的字符串参数创建对应的代码沙箱实例
   */
  public static CodeSandbox newInstance(String type){
  
          CodeSandboxEnum value = CodeSandboxEnum.getEnumByValue(type);
  
          switch (value){
              case EXAMPLE:
                  return new ExampleCodeSandbox();
              case REMOTE:
                  return new RemoteCodeSandbox();
              case THIRDPARTY:
                  return new ThirdPartySandbox();
              default:
                  throw new BusinessException(ErrorCode.OPERATION_ERROR,"请求沙箱不存在");
          }
      }
  ```

- **通过读取yml配置使用哪种代码沙箱**
  - 调用工厂时把读取的type传进去就行

- 在沙箱调用前后加日志，输出request和response信息
  - 静态代理实现
  - AOP实现
    - 注意：切入点中@within用于匹配方法级别的注解，@annotation用于匹配方法级别的注解

















# 项目拓展

- 通过数+提交数，动态地设置题目难度标签

































































