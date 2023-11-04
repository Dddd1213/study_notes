# RabbitMQ

[TOC]

# 01

## 简介

- 同步：如视频电话
- 异步：如微信聊天
- （docker里下的版本：3.8）

## 异步调用

- 消息发送者，消息代理，消息接受者（服务提供方）
- 缺点：不能立刻得到结果；不知道下游业务是否执行成功；业务安全依赖于代理的可靠性

![image-20231103175340923](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231104195648300-330155497.png)

## 技术选型

- MQ：message queue，消息队列
- 四种MQ

## RabbitMQ架构

- virtualhost：虚拟主机，隔离作用
- 交换机：路由和转发消息，不存储

![image-20231103200428539](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231104195647697-1376885323.png)



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

![image-20231103213802969](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231104195647250-2081228598.png)

## 订阅模式

- 问题：一个交换机绑定了多个队列abcde，但是有时候只需要发给a，或者只需要发cde
  - 使用derect类型的交换机
  - 使用bindingKey，发布者发布的时候指定bingdingKey，交换机把消息发给有指定bingdingkey的队列

![image-20231103213846773](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231104195646796-1947299316.png)

### 通配符订阅

- 使用Topic，rotingkey可以是多个单词的列表，且以点分隔
- 指定bingdingkey时可以使用通配符
  - \# 代指0个或多个单词
  - \*代指一个单词

![image-20231103213905773](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231104195646363-1104031627.png)



## 声明队列交换机

- SpringAMQP中提供的类
  - Queue：声明队列
  - Exchange：声明交换机
  - Binding：声明队列和交换机的绑定关系

- new一个对象和用builder都可以
- 队列和交换机的声明一般在消费者这边

- 选第一个![image-20231104125242203](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231104195645962-640687768.png)


### 基于Bean声明

- fanout

```java
@Configuration
public class FanoutConfiguration {

    @Bean
    public FanoutExchange fanoutExchange(){
        return new FanoutExchange("yangmao.fanout2");
    }

    @Bean
    public Queue fanoutQueue3(){
//        QueueBuilder.durable("fanout.q3");
        return new Queue("fanout.q3");
    }

    //参数注入方式绑定
    @Bean
    public Binding fanoutBinding3(Queue fanoutQueue3,FanoutExchange fanoutExchange){
        return BindingBuilder.bind(fanoutQueue3).to(fanoutExchange);
    }

    @Bean
    public Queue fanoutQueue4(){
//        QueueBuilder.durable("fanout.q3");
        return new Queue("fanout.q4");
    }

    //直接方法调用式绑定，上面创建过了，其实这里调用的也是已经创建好的bean
    @Bean
    public Binding fanoutBinding4(){
        return BindingBuilder.bind(fanoutQueue4()).to(fanoutExchange());
    }
}
```

### 基于注解声明

- 问题：Direct每绑定一个key都要声明一个bean，麻烦

- 解决：基于注解，能直接创建+绑定
- 导包别导错，这里面全是注解类型，上面基于bean的全是类类型

```java
@RabbitListener(bindings = @QueueBinding(
    value = @Queue(name = "direct.queue1"),
    exchange = @Exchange(name = "hmall.direct", type = ExchangeTypes.DIRECT),
    key = {"red", "blue"}
))
public void listenDirectQueue1(String msg){
    System.out.println("消费者1接收到direct.queue1的消息：【" + msg + "】");
}

@RabbitListener(bindings = @QueueBinding(
    value = @Queue(name = "direct.queue2"),
    exchange = @Exchange(name = "hmall.direct", type = ExchangeTypes.DIRECT),
    key = {"red", "yellow"}
))
public void listenDirectQueue2(String msg){
    System.out.println("消费者2接收到direct.queue2的消息：【" + msg + "】");
}
```



## 消息转换器

- 向queue中发送对象，queue中收到乱码

- 原因：传输的时候会转成字节传输，且jdk自带的序列化报用
- 解决：用JSON序列化代替默认的jdk序列化
- 导包

```xml
<dependency>
    <groupId>com.fasterxml.jackson.dataformat</groupId>
    <artifactId>jackson-dataformat-xml</artifactId>
</dependency>
```

- 声明

```java
@Bean
public Jackson2JsonMessageConverter jacksonMessageConvertor(){
    return new Jackson2JsonMessageConverter();
}
```

- 生产者和消费者两边都得有



# 03

- 消息可靠性：一个消息被发出之后，至少被消费一次

## 生产者可靠性

### 失败重连

- 场景：连接超时
- 配置超时时间和retry

```yaml
spring:
  rabbitmq:
    connection-timeout: 1s # 设置MQ的连接超时时间
    template:
      retry:
        enabled: true # 开启超时重试机制
        initial-interval: 1000ms # 失败后的初始等待时间
        multiplier: 1 # 失败后下次的等待时长倍数，下次等待时长 = initial-interval * multiplier
        max-attempts: 3 # 最大重试次数
```

- 阻塞式重试，一般初始等待时间配个200mm就够了

### 生产者确认

- 场景：MQ收到消息后返回确认消息
- 几种情况
  - 当消息投递到MQ，但是路由失败时，通过**Publisher Return**返回异常信息，同时返回ack的确认信息，代表投递成功
  - 临时消息投递到了MQ，并且入队成功，返回ACK，告知投递成功
  - 持久消息投递到了MQ，并且入队完成持久化，返回ACK ，告知投递成功
  - 其它情况都会返回NACK，告知投递失败
- 其中`ack`和`nack`属于**Publisher Confirm**机制，`ack`是投递成功；`nack`是投递失败。而`return`则属于**Publisher Return**机制。

![image-20231104141911034](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231104195645539-1364021842.png)





### 生产者确认的实现

#### 配置文件

```yaml
spring:
  rabbitmq:
    publisher-confirm-type: correlated # 开启publisher confirm机制，并设置confirm类型，默认null不开启，还有simple同步阻塞等待
    publisher-returns: true # 开启publisher return机制
```

#### 配置类

- **returncallback**
  - 当你发送一条消息到RabbitMQ，但是消息无法被正确路由到队列时，RabbitMQ会将消息返回给生产者。
  - Return Callback用于处理这种无法被路由到队列的消息，你可以在Return Callback中定义自己的处理逻辑，比如记录日志或者重新发送消息。
  - 
  - 每个`RabbitTemplate`只能配置一个`ReturnCallback`，因此我们可以在配置类中统一设置。
  - 这里也可以用Autoware实现

```java
@Slf4j
@Configuration
public class MqConfirmConfig implements ApplicationContextAware {

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        RabbitTemplate rabbitTemplate = applicationContext.getBean((RabbitTemplate.class));
        //配置回调
        rabbitTemplate.setReturnsCallback(new RabbitTemplate.ReturnsCallback() {
            @Override
            public void returnedMessage(ReturnedMessage returnedMessage) {
                log.debug("收到消息的return callback");
                
            }
        });
    }
}
```

- **ConfirmCallback**
  - 用于确认消息是否被成功接收并投递到队列中。
  - 由于每个消息发送时的处理逻辑不一定相同，因此ConfirmCallback需要在每次发消息时定义。调用RabbitTemplate中的convertAndSend方法时，多传递一个参数：CorrelationData
  - `id`：消息的唯一标示，MQ对不同的消息的回执以此做判断，避免混淆
  - `SettableListenableFuture`：回执结果的Future对象

```java
@Test
void testPublisherConfirm() {
    // 1.创建CorrelationData
    CorrelationData cd = new CorrelationData(UUID.randomUUID().toString()));
    // 2.给Future添加ConfirmCallback
    cd.getFuture().addCallback(new ListenableFutureCallback<CorrelationData.Confirm>() {
        @Override
        public void onFailure(Throwable ex) {
            // 2.1.Future发生异常时的处理逻辑，基本不会触发
            log.error("send message fail", ex);
        }
        @Override
        public void onSuccess(CorrelationData.Confirm result) {
            // 2.2.Future接收到回执的处理逻辑，参数中的result就是回执内容
            if(result.isAck()){ // result.isAck()，boolean类型，true代表ack回执，false 代表 nack回执
                log.debug("发送消息成功，收到 ack!");
            }else{ // result.getReason()，String类型，返回nack时的异常描述
                log.error("发送消息失败，收到 nack, reason : {}", result.getReason());
            }
        }
    });
    // 3.发送消息
    rabbitTemplate.convertAndSend("hmall.direct", "q", "hello", cd);
}
```

- 生产者确认需要额外的网络和系统资源开销，如果一定要使用，可以不开启publisher-return机制



## MQ的可靠性

### 数据持久化

- 交换机持久化：Durable持久，Transient临时

- 队列持久化
- 消息持久化：1是临时，2是持久
  - 非持久化，容易发生pageout，有段时间处于阻塞状态，无法接收消息

```java
@Test
void testPageOut(){

    Message message = MessageBuilder
            .withBody("hello".getBytes(StandardCharsets.UTF_8))
            .setDeliveryMode(MessageDeliveryMode.NON_PERSISTENT).build();

    rabbitTemplate.convertAndSend("simple.queue",message);
}
```

### LazyQueue

- 接收到消息后直接保存到磁盘（而不是内存），内存中默认只保存2048条
- 3.12后默认LazyQueue

- 代码配置

```java
@Bean
public Queue lazyQueue(){
    return QueueBuilder
            .durable("lazy.queue")
            .lazy() // 开启Lazy模式
            .build();
}
```

- （或）注解方式

```java
@RabbitListener(queuesToDeclare = @Queue(
        name = "lazy.queue",
        durable = "true",
        arguments = @Argument(name = "x-queue-mode", value = "lazy")
))
public void listenLazyQueue(String msg){
    log.info("接收到 lazy.queue的消息：{}", msg);
}
```



## 消费者可靠性

### 消费者确认

- 三种情况
  - ack：消息已被接受且处理，从队列中删除
  - nack：再次发送
  - reject：这个消息有问题！从队列中删除！

- SpringAMQP中已实现
  - `none`：不处理。即消息投递给消费者后立刻ack，消息会立刻从MQ删除。非常不安全，不建议使用
  - `manual`：手动模式。需要自己在业务代码中调用api，发送`ack`或`reject`，存在业务入侵，但更灵活
  - `auto`：自动模式。SpringAMQP利用AOP对我们的消息处理逻辑做了环绕增强，当业务正常执行时则自动返回`ack`.  当业务出现异常时，根据异常判断返回不同结果：
    - 如果是**业务异常**，会自动返回`nack`；
    - 如果是**消息处理或校验异常**，自动返回`reject`;

- 在配置文件中设置

  ```xml
  spring:
    rabbitmq:
      listener:
        simple:
          acknowledge-mode: none # 不做处理
  ```

  

### 消费失败处理

- 默认：如果nack不断重试直到成功，会浪费时间且加大MQ压力
- 解决：用spring的重试机制，先在本地重试，设置重试时间和最大重试次数

```yaml
spring:
  rabbitmq:
    listener:
      simple:
        retry:
          enabled: true # 开启消费者失败重试
          initial-interval: 1000ms # 初识的失败等待时长为1秒
          multiplier: 1 # 失败的等待时长倍数，下次等待时长 = multiplier * last-interval
          max-attempts: 3 # 最大重试次数
          stateless: true # true无状态；false有状态。如果业务中包含事务，这里改为false
```

- 如果多次重试依然失败：`MessageRecovery`接口
  - `RejectAndDontRequeueRecoverer`：重试耗尽后，直接`reject`，丢弃消息。默认就是这种方式 
  - `ImmediateRequeueMessageRecoverer`：重试耗尽后，返回`nack`，消息**重新入队** 
  - `RepublishMessageRecoverer`：重试耗尽后，将失败消息投递到指定的交换机 ，然后扔到一个异常队列，进行人工处理（优雅！）

#### 第三种方式做法

- 在consumer服务的Config包下，建一个ErrorConfiguration类，定义处理失败消息的交换机和队列

  ```java
  package com.itheima.consumer.config;
  
  import org.springframework.amqp.core.Binding;
  import org.springframework.amqp.core.BindingBuilder;
  import org.springframework.amqp.core.DirectExchange;
  import org.springframework.amqp.core.Queue;
  import org.springframework.amqp.rabbit.core.RabbitTemplate;
  import org.springframework.amqp.rabbit.retry.MessageRecoverer;
  import org.springframework.amqp.rabbit.retry.RepublishMessageRecoverer;
  import org.springframework.context.annotation.Bean;
  
  @Configuration
  //当某个retry机制开启时才生效
  @ConditionalOnProperty(name = "spring.rabbitmq.listener.simple.retry.enabled", havingValue = "true")
  public class ErrorMessageConfig {
      @Bean
      public DirectExchange errorMessageExchange(){
          return new DirectExchange("error.direct");
      }
      @Bean
      public Queue errorQueue(){
          return new Queue("error.queue", true);
      }
      @Bean
      public Binding errorBinding(Queue errorQueue, DirectExchange errorMessageExchange){
          return BindingBuilder.bind(errorQueue).to(errorMessageExchange).with("error");
      }
  
      @Bean
      public MessageRecoverer republishMessageRecoverer(RabbitTemplate rabbitTemplate){
          return new RepublishMessageRecoverer(rabbitTemplate, "error.direct", "error");
      }
  }
  ```


### 业务幂等性

- 指同一个业务，执行一次和执行多次结果一样
- 实际业务中，由于以外可能经常会导致业务被重复执行的情况

#### 方案一

- 给消息设置一个唯一id，发送消息时携带该id
- 消费者收到后处理业务，业务成功后将id保存到数据库
- 如果下次收到相同消息，若数据库中存在，则不再处理

- 实现：SpringAMQP的MessageConverter自带了MessageID的功能，我们只要开启这个功能即可。

  - 我们前面配置了一个jackson消息转换器，可以在这个里面加

  ```java
  @Bean
  public MessageConverter messageConverter(){
      // 1.定义消息转换器
      Jackson2JsonMessageConverter jjmc = new Jackson2JsonMessageConverter();
      // 2.配置自动创建消息id，用于识别不同消息，也可以在业务中基于ID判断是否是重复消息
      jjmc.setCreateMessageIds(true);
      return jjmc;
  }
  ```

- 缺点：查数据库很慢，对性能有影响

#### 方案二

- 基于业务
  - eg. 处理消息的业务逻辑是把订单状态从未支付修改为已支付。因此我们就可以在执行业务时判断订单状态是否是未支付，如果不是则证明订单已经被处理过，无需重复处理。
- 举例实现：（红线圈起来的那一行相当于上面三行，且不存在并发问题）

![image-20231104163924719](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231104195645109-922253599.png)

### 兜底方案

- 可以在交易服务中设置定时任务，定期主动查询订单支付状态，确保订单支付状态最后的一致性



# 04

## 延迟消息

- 使用场景

![image-20231104164857446](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231104195644662-1575178051.png)



### 死信交换机

- 死信 dead letter
  - 消费者使用`basic.reject`或 `basic.nack`声明消费失败，并且消息的`requeue`参数设置为false
  - 消息是一个过期消息，超时无人消费
  - 要投递的队列消息满了，无法投递
- 死信交换机（DLX）：把队列中的死信投递到这个交换机中
- 给一个消息设置一个过期时间，simple.queue中不绑定消费者，消息过期后变成死信进入死信交换机，然后再被消费者消费，从而实现延迟消息
  - 发送时，加一个new MessageProcessor()，然后在里面message.getMessageProperties.setExpiration("1000")

![image-20231104165238539](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231104195644157-329649276.png)



## 延迟消息插件

- 官方给出一种特定的交换机

- 先在docker的mq里安装delay插件
- 启动插件

```
docker exec -it mq rabbitmq-plugins enable rabbitmq_delayed_message_exchange
```

### 声明延迟交换机

- 注解，delayed = true

```java
@RabbitListener(bindings = @QueueBinding(
        value = @Queue(name = "delay.queue", durable = "true"),
        exchange = @Exchange(name = "delay.direct", delayed = "true"),
        key = "delay"
))
public void listenDelayMessage(String msg){
    log.info("接收到delay.queue的延迟消息：{}", msg);
}
```

- 用bean

```java
 @Bean
    public DirectExchange delayExchange(){
        return ExchangeBuilder
                .directExchange("delay.direct") // 指定交换机类型和名称
                .delayed() // 设置delay的属性为true
                .durable(true) // 持久化
                .build();
    }
```

### 发送延迟消息

- setDelay

```java
@Test
void testPublisherDelayMessage() {
    // 1.创建消息
    String message = "hello, delayed message";
    // 2.发送消息，利用消息后置处理器添加消息头
    rabbitTemplate.convertAndSend("delay.direct", "delay", message, new MessagePostProcessor() {
        @Override
        public Message postProcessMessage(Message message) throws AmqpException {
            // 添加延迟消息属性
            message.getMessageProperties().setDelay(5000);
            return message;
        }
    });
}
```

### 缺点

- cpu密集，对cpu压力大
- 优化：
- 用户下单后的第10秒、20秒、30秒、45秒、60秒、1分30秒、2分、...30分分别设置延迟消息，如果提前发现订单已经支付，则后续的检测取消即可。这样就可以有效避免对MQ资源的浪费了。

![image-20231104191909857](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231104195643672-1322030958.png)
