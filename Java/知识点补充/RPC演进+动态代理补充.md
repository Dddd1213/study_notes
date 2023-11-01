# RPC演进

- RPC Remote Procedure call，远程方法调用，重点在方法调用（不支持对象的概念）
- 单机到分布式 -> 分布式通信 -> 最基本：二进制数据传输TCP/IP

- 场景：server端连数据库。client把id传给server，server中调用UserService（继承IUserService接口）中的findUserById()，然后从server中获得user对象。

## 01

- Java基础里的socket编程
- 制定监听端口，用inputstream，outputstream写进去读出来

## 02

- 封装网络传输部分
- 建个Stub（客户端）类，作为代理
- 把client端原来的getbyid方法放到Stub里（相当于把跟网络相关部分拎出来封装成一个类），这样client中就只用new Stub类，然后调用stub里的方法

## 03

- Stub提供一个IUserService，可以在client得到一个UserService对象，然后远程访问findUserById()
- 基于动态代理，invoke里放联网，写，读，最后返回读到的user
- 缺点：这一版里还是硬往服务器端传的id，相当于只适配findUserById()

## 04

- 基于03，在invoke里将方法名(.getName)，参数类型(.getParameterTypes)（因为有可能重写）和参数（args）传给server，然后在server那边再通过方法名和参数类型找到调用的方法
- 服务器端改进：读入方法名，参数类型，参数；通过反射 service.getClass.getMethod(methodName, parameterTypes)找到该方法method，然后用method.invoke(service, args)调用
- **解决了对同一接口中不同方法的支持，可以随意增添新的方法；**
- 缺点：返回值还没处理；传回来的user还是id name拆解的，如果user定义变了，这个代码还是得变

## 05

- server端writeObject（user），直接写入一个对象
- **（即返回值用object封装，支持任意对象返回值）**



## 06

- 把getStub的返回值改成Object（原来是User），getStub加一个Class类型的参数clazz
- 在client的main中调用的时候，把需要的类.Class传到getStub里
- 在getStub的invoke中，把clazz也一起传给server
- 在server中，先找类，再在类中找方法



## 07

- （他这里好像就是换了个对象测了一下06）



## 08

- 使用序列化框架Hessian



课程：[马士兵教育专属 马士兵老师讲：RPC的演化过程，36行代码透彻解析RPC，听不懂你打我！_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1zE41147Zq/)



## 补充：动态代理

- 分析：test中，proxy.sing调用sing方法，会调动invoke，此时method为sing，然后return method.invoke()，调bigstar.sing，把bigstar.sing的返回值返回给method.invoke()，再返到test中的String sing这里。
- Test类

```java
public class Test {
    public static void main(String[] args) {
        BigStar star = new BigStar("xiaoyang");
        Star proxy = ProxyUtil.createProxy(star);
        String sing = proxy.sing("17 years");
        System.out.println(sing);
        proxy.dance();
    }
}
```

- Star接口类

```java
public interface Star {
    String sing(String name);
    void dance();
}
```

- BigStar类

```java
public class BigStar implements Star{
    private String name;
    public BigStar(String name){
        this.name = name;
    }
    public String sing(String name){
        System.out.println(this.name+"正在唱："+name);
        return "thank you";
    }
    public void dance(){
        System.out.println(this.name+"正在跳舞");
    }
}
```

- ProxyUtil类

```java
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

public class ProxyUtil {
    public static Star createProxy(BigStar bigStar){

        /*
        * (ClassLoader loader,指定类加载器（直接用当前类）
        * Class<?>[] interfaces,指定生成的代理有哪些方法，接口数组
        * InvocationHandler h) 指定生成的代理对象要干什么事情
        *
        * */
        Star o = (Star)Proxy.newProxyInstance(ProxyUtil.class.getClassLoader(),
                new Class[]{Star.class}, new InvocationHandler() {
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        if(method.getName().equals("sing")){
                            System.out.println("准备话筒");
                        }
                        else if(method.getName().equals("dance")){
                            System.out.println("准备场地");
                        }
                        return method.invoke(bigStar,args);
                    }
                });

        return o;
    }
}
```



















































