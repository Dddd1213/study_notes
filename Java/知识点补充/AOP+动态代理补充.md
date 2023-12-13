# AOP+动态代理

[TOC]

## 1 动态代理

### 1.1 jdk

- 要求目标类与代理类实现相同的接口，若目标类不存在接口，则不行

#### 反射

- Method类

  - 使用Method可以执行某个方法

  - 参数：方法名，参数类型.class

  - 用法：

    ![image-20231212214520198](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231212214520198.png)

- invoke

  - 参数：object，表示目标**对象**；args，方法执行的参数值

  - 用法：

    ![image-20231212214534329](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231212214534329.png)

#### InvocationHandler接口

- 里面就一个invoke()，里面写代理对象要执行的功能代码，包括**调用目标方法**，和**增加的功能**
- 方法原型：public Object **invoke(Object proxy, Method method, Object[] args)**
- proxy：jdk创建的代理对象，无需赋值
  - 静态方法newProxyInstance()，创建代理对象（不需要自己new）
    - public static Object newProxyInstance(ClassLoader loader,
                                            Class<?>[] interfaces,
                                            InvocationHandler h)
    - ClassLoader loader：指定类加载器，使用反射来获取**目标对象**的classloader，比如a.getClass().getClassLoader()
    - Class<?>[] interfaces：目标对象所实现的接口，也是反射获取
    - InvocationHandler h：我们自己写的代理类要完成的功能
    - 通过以上三个参数加工得到代理对象作为返回值

- method：目标类中的方法，无需赋值（jdk来提供method对象）
  - 注意！Method中也有个**invoke(目标对象，参数)**方法，是用来执行目标对象的方法的！！注意区分！！！

- Object[] args：目标类中方法的参数，无需赋值

- 用法：创建类实现InvationHandler接口，重写invoke，把原来静态代理类中要完成的功能写在里面



### 1.2 cglib

- 原理是生成目标类的子类，要求目标类必须能被继承，不能是final的



### 1.3 jdk实现动态代理

- 实现动态代理的步骤:
  - 创建接口，定义目标类要完成的功能2．创建目标类实现接口
  - 创建InvocationHandler接口的实现类，在invoke方法中完成代理类的功能
    - 调用目标方法
    - 增强功能
  - 使用Proxy类的静态方法，创建代理对象，并把返回值转为接口类型



### 1.4 动态代理相对于静态代理的好处

- 可以一次代理**多个接口**

  - 如果是静态代理，每一个代理类就只能代理一个接口

  ```java
  public interface ServiceA {
      void methodA();
  }
  public interface ServiceB {
      void methodB();
  }
  public class MyHandler implements InvocationHandler {
      private Object target;
  
      public MyHandler(Object target) {
          this.target = target;
      }
      @Override
      public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
          System.out.println("DynamicProxy is doing something before method invocation.");
          Object result = method.invoke(target, args);
          System.out.println("DynamicProxy is doing something after method invocation.");
          return result;
      }
  }
  // 创建动态代理对象
  ServiceA serviceA = new MyHandler(new ServiceAImpl()).createProxy();
  serviceA.methodA();
  
  ServiceB serviceB = new MyHandler(new ServiceBImpl()).createProxy();
  serviceB.methodB();
  ```

  看这篇博客：[动态代理相较于静态代理的优势_动态代理比静态代理好在哪里-CSDN博客](https://blog.csdn.net/m0_49349350/article/details/120677279?ops_request_misc=%7B%22request%5Fid%22%3A%22170244312616800182157842%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=170244312616800182157842&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~baidu_landing_v2~default-1-120677279-null-null.142^v96^pc_search_result_base2&utm_term=动态代理相对于静态代理的好处&spm=1018.2226.3001.4187)



















