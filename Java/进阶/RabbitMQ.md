# RabbitMQ

[TOC]

# 01

## 简介

- 同步：如视频电话
- 异步：如微信聊天

## 异步调用

- 消息发送者，消息代理，消息接受者（服务提供方）
- 缺点：不能立刻得到结果；不知道下游业务是否执行成功；业务安全依赖于代理的可靠性

![image-20231103175340923](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231103175340923.png)

## 技术选型

- MQ：message queue，消息队列
- 四种MQ

## RabbitMQ架构

- virtualhost：虚拟主机，隔离作用
- 交换机：路由和转发消息，不存储

![image-20231103200428539](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231103200428539.png)



# 02

## Spring AMQP

- ```java
  <!--AMQP依赖，包含RabbitMQ-->
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-amqp</artifactId>
  </dependency>
  ```

- 配置文件（收发消息的配置文件要一致）

```yaml
spring:
  rabbitmq:
    host: 192.168.150.101 # 你的虚拟机IP
    port: 5672 # 端口
    virtual-host: /hmall # 虚拟主机
    username: hmall # 用户名
    password: 123 # 密码
```

- 发送消息

```java
@SpringBootTest
public class SpringAmqpTest {

    @Autowired
    private RabbitTemplate rabbitTemplate;

    @Test
    void test1(){
        rabbitTemplate.convertAndSend("simple.queue","hello amqp!");
    }

}
```

- 接收消息

```java
@Slf4j
@Component
public class MqListener {

    @RabbitListener(queues="simple.queue")
    public void listenSimpleQueue(String msg){

        System.out.println("消费者收到"+msg);

    }
}
```

## 消息推送限制

- 场景：在一个队列上绑定多个消费者
  - 可以加快消息处理的速度

![](https://cdn.nlark.com/yuque/0/2023/jpeg/27967491/1687261956699-4b3c9999-ee86-4dda-a795-1ea5f4f9eef3.jpeg)

- 默认：会平均轮询投递给队列上的每个消费者
- 配置：

```yml
spring:
  rabbitmq:
    listener:
      simple:
        prefetch: 1 # 每次只能获取一条消息，处理完成才能获取下一个消息
```



## 交换机

- 一个消息要被多个微服务接收。如果只用队列，一个消费者消费完就没了。

- 用交换机

  ![](https://cdn.nlark.com/yuque/0/2023/jpeg/27967491/1687264784359-de7cbc4a-ec60-461d-a6a4-3474ba52e0d0.jpeg)



- 什么时候需要一个队列绑定多个消费者？
  - 被绑定的消费者是集群，它们功能一样的
- 种类
  - **Fanout**：广播，将消息交给所有绑定到交换机的队列。我们最早在控制台使用的正是Fanout交换机
  - **Direct**：订阅，基于RoutingKey（路由key）发送给订阅了消息的队列
  - **Topic**：通配符订阅，与Direct类似，只不过RoutingKey可以使用通配符
  - **Headers**：头匹配，基于MQ的消息头匹配，用的较少。
- 

### 广播模式

![image-20231103213802969](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231103213802969.png)

## 订阅模式

- 问题：一个交换机绑定了多个队列abcde，但是有时候只需要发给a，或者只需要发cde
  - 使用derect类型的交换机
  - 使用bindingKey，发布者发布的时候指定bingdingKey，交换机把消息发给有指定bingdingkey的队列

![image-20231103213846773](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231103213846773.png)

### 通配符订阅

- 问题：derect只能绑定唯一的bingdingkey
  - 使用Topic，rotingkey可以是多个单词的列表，且以点分隔
  - 指定bingdingkey时可以使用通配符
    - \# 代指0个或多个单词
    - \*代指一个单词

![image-20231103213905773](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231103213905773.png)



## 声明队列交换机

- SpringAMQP中提供的类
  - Queue：声明队列
  - Exchange：声明交换机
  - Binding：声明队列和交换机的绑定关系







