# JUC

[TOC]

# 01

## JUC

- java.util.concurrent工具包的简称

## 进程和线程

### 线程的状态

- Thread中有state枚举类

### wait和sleep

- wait是Object方法，sleep是thread的静态方法
- sleep不会释放锁（抓住锁睡觉），也不需要占用锁；wait会释放锁，调用它的前提是当前线程占有锁（即代码在synchronized中）
- 都能被interrupted中断

### 并发和并行

- 并行：多个任务同时执行，最后汇总（比如一边烧水一边拖地）
- 并发：多个线程同时访问一个资源（春运抢票，电商秒杀）

## 管程 

- monitor，是一种同步机制，保证同一个时间只能有一个线程访问被保护的数据或者代码

- jvm同步基于进入和退出，是使用管程对象实现的

## 用户线程和守护线程

- 用户线程：自定义线程
- 守护线程：用在后台中特殊的线程（垃圾回收）





# 02

## Future

- future的核心方法
  - get，get(long time) 同步地获取任务的执行结果
    - get会**阻塞等待**该线程执行完后再返回值
    - get(long time) 阻塞等待指定时间后，就会抛出超时异常
  - cacel 取消
  - iscancelled，isDone 查询状态
    - isDone 轮询，虽然可以避免阻塞，但是会耗费cpu资源
- 异步并行计算，为主主线程开一个分支任务，过一阵再通过future获取执行结果
- runnable接口，callable接口，future 接口，futureTask实现类（构造注入callable）
- 多线程！有返回！异步任务！
  - runnable中的run没有返回
  - callable接口中call返回string
- 结合线程池

```java
public static void main(String[] args) throws ExecutionException, InterruptedException {

        ExecutorService threadPool = Executors.newFixedThreadPool(3);

        FutureTask futureTask1 = new FutureTask<String>(()->{

            TimeUnit.MILLISECONDS.sleep(500);
            return "task1 over";
        });
//        Thread t1 = new Thread(futureTask1,"t1");
//        t1.start();

        threadPool.submit(futureTask1);
        FutureTask futureTask2 = new FutureTask<String>(()->{

            TimeUnit.MILLISECONDS.sleep(500);
            return "task2 over";
        });
        threadPool.submit(futureTask2);

        FutureTask futureTask3 = new FutureTask<String>(()->{

            TimeUnit.MILLISECONDS.sleep(500);
            return "task3 over";
        });

        threadPool.submit(futureTask3);
        System.out.println(futureTask1.get());
        System.out.println(futureTask2.get());
        System.out.println(futureTask3.get());

        threadPool.shutdown();
    }
```

## CompletableFuture

- Future进阶版！更多功能版！解决痛点版！
  - 扩展实现了CompletionStage接口
  - 减少阻塞和轮询
  - 可以传入回调对象

- 四个核心静态方法

  - runAsync 无返回值
    - 指定/不指定线程池，不指定就用默认的
  - supplyAsync 有返回值
    - 指定/不指定线程池，不指定就用默认的

- 回调

  - whenComplete
  - 注意，默认线程池是守护线程，mian结束后守护线程也能结束，所以最好用自定义线程池

  ```java
  public static void main(String[] args) throws ExecutionException, InterruptedException {
  
      CompletableFuture.supplyAsync(()->{
          System.out.println("进入cf1");
          System.out.println(Thread.currentThread().getName());
          try {
              TimeUnit.SECONDS.sleep(1);
          } catch (InterruptedException e) {
              e.printStackTrace();
          }
          int result = ThreadLocalRandom.current().nextInt(10);
          return result;
      }).whenComplete((v , e)->{
          //v为前面return的result，e为抛出的异常
          if(e==null){
              System.out.println("-------计算完成，更新系统"+v);
          }
      }).exceptionally(e -> {
          e.printStackTrace();
          System.out.println("异常情况"+e.getCause()+"\t"+e.getMessage());
          return null;
      });
      System.out.println("主线程先去忙忙别的");
  ```



## 函数式编程

- ![image-20231123122421570](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231123122421570.png)



- 流式编程

  ```java
  public List<String> getPrice(List<NetMall> list,String productName){
      return list.stream()
              .map(netMall -> 
                      String.format(productName+"in %s price is %.2f",
                              netMall.getNetMallName(),
                              netMall.calcPrice(productName)))
              .collect(Collectors.toList());
  }
  ```



