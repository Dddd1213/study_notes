# Java基础知识

[TOC]

已学习：374~459, 615~641，662~688



## 第08章 面向对象编程（中级部分）

### 306~318 多态









## 第10章 面向对象编程（高级部分）

### 374~382 static 类变量和类方法

- 类方法：不用创建对象可直接通过类名调用方法
- 静态方法只能访问静态成员
- 普通成员方法 既可以访问 静态成员，也可以访问非静态成员



### 383~385 main



### 386~391 代码块

- 只有方法体的方法，在加载类的时候隐式地调用
- 静态/普通代码块
- 相当于另一种形式的构造器，如果多个构造器（重载）中都有重复的语句，可以抽取到初始化代码块中
- 静态代码块只会在**类被加载的时候执行一次**，普通代码块**每创建一个对象就会执行一次**
  - 类被加载的情况：创建对象实例时；创建子类时父类也会被加载；使用类的静态成员时
- ![image-20230714210523636](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231016184705569-534409781.png)



### 392~393 单例模式

- 饿汉式
- 懒汉式



### 394~397 final



### 403~412 接口

- 接口中的属性都是相当于被 **public static final** 修饰的
- 多态
  - 接口类型变量可以指向实现了该接口的对象实例 。
  - instanceof 判断运行类型 



### 413~424 内部类

1. 类的五大成员：属性、方法、构造器、代码块、内部类。
2. 分类：局部内部类，匿名内部类；成员内部类，静态内部类。
3. 内部类的优点：
   - 每个内部类都能独立的继承一个接口的实现，所以无论外部类是否已经继承了某个(接口的)实现，对于内部类都没有影响。内部类使得多继承的解决方案变得完整；
   - 方便将存在一定逻辑关系的类组织在一起，又可以对外界隐藏；
   - 方便编写事件驱动程序；
   - 方便编写线程代码。

4. **局部内部类**：定义在外部类的局部位置，通常在方法中

5. **匿名内部类（重要）**：

   - 没有类名
   - 作用：简化开发（比如一个类要继承一个接口且只需要用一次，就可以使用匿名内部类）

   ```java
   public static void main(){
   IA tiger = new IA(){
       @override
       public void cry(){
           System.out.println("老虎叫...");
       }
   };
   tiger.cry();
   }
   interface IA{
       public void cry();
   }
   ```

   - **将匿名内部类作为实参传递**，简洁高效

     ```java
     package com.example.demo.controller;
     public class InnerClassExercise02 {
         public static void main(String[] args) {
             Cellphone cellphone = new Cellphone();
             cellphone.alarmclock(new Bell(){
                 @Override
                 public  void ring()
                 {
                     System.out.println("懒猪起床了...");
                 }
             });
             cellphone.alarmclock(new Bell(){
                 @Override
                 public void ring(){
                     System.out.println("小伙伴们上课了");
                 }
             }
             );
         }
     }
     class Cellphone{
         public void alarmclock(Bell bell)
         {
             bell.ring();
         }
     }
     interface Bell{
         void ring();
     }
     ```

6. **成员内部类**

   - 可以调用外部类的所有成员；外部类若想使用成员内部类 ，则需要创建相应的对象。
   - 外部其他类调用成员内部类的方式：

   ```java
   Outer08 outer08 = new Outer08();
   //第一种
   Outer08.Inner08 inner08 = outer08.new Inner08();
   //第二种
   在Outer08中写一个getInner08()方法，方法中return new Innter08;
   ```

7. 静态内部类：放在成员位置



## 第11章 枚举和注解

### 425~432 Enum枚举

1. 自定义枚举
   - 将构造器私有化防止直接new
   - 去掉set方法防止属性被修改
   - 在类的内部直接创建固定的对象**public static final**

2. Enum枚举
   - 使用关键字Enum替代class，该类会隐式地继承Enum，不能再继承其他类了
   - 常量名（实参列表）
   - 如果有多个常量（对象），使用逗号间隔
   - 如果使用无参构造器创建对象，则可以省略（）
   - 枚举对象必须放在行首

![image-20230716172453064](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231016184705036-615127034.png)



### 433~436 注解

- @override 子类是否成功重写父类方法，如果没有成功重写则编译错误
- @Deprecated 表示该元素已过时，不推荐使用但仍能使用。做版本升级过渡。
- @SuppressWarnings 诉编译器忽略此处代码产生的警告。



## 第12章 异常

### 444~459 异常处理

1. 两大类
   - Error 严重错误，如栈溢出等
   - Exception 一般性问题，如空指针访问，网络连接中断等（编译/运行异常）

2. 五大运行异常：NullPointerException、ArithmeticException、ArrayIndexOutOfBoundException、ClassCastException、NumberFormatException

3. Try catch

- 如果异常发生了，异常发生后的模块都不会执行，直接进入catch。
- 如果没有发生，则不会进入catch。
- 如果不管怎样都执行，则用finally。
- IOException: 包括`FileNotFoundException`，`EOFException`，`SocketException`，`InterruptedIOException`、`EOFException`、`SocketTimeoutException`等等。

## 第13章 常用类（460~498）

### 460~463 Wrapper类

1. 八大包装类

   ![image-20230808141513268](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231016184704611-475900379.png)



## 第14章 集合 (499~553)

### List (505~517)

1. List的三种遍历方式

   - iterator

     ```java
     iterator iter = col.iterator();
     while(iter.hasNext())
     {
         Object o = iter.next();
     }
     ```

   -  增强for

     ```java
     for(Object o : col)
     {
     }
     ```

   -  普通for

     ```java
     for(int i=0;i<list.size();i++)
     {
     	Object object = list.get(i);
     	System.out.println(object);
     }
     ```

     





## 第15章 泛型（554~568）

### 泛型

- 作用：在类声明时通过一个标识表示类中某个属性的类型

  ```java
  class Person<T>{
      T s; //T表示s的数据类型
  }
  ```

  



### 自定义泛型



### JUnit

- 单元测试框架
- 在方法前加@Test注解





## 第19章 IO流

### 615~638 IO流

1. 流的分类：字节流（8 bit）二进制文件，字符流；输入/输出流；节点流，处理流/包装流。

2. **FileInputStream** ：读取字节数据，适用于**文本文件、二进制文件**等多类型文件
   
   - .read()：读取一个数据字节，返回下一个数据字节（**int**型），如果已经达到末尾则返回-1
   - .read(byte[] b)：读取b.length()个字节数组中，如果读取正常则返回实际读取的字节数，末尾-1。处理中文的时候将b设置为3的倍数。
   - fileInputStream.close() ：用完之后记得关闭
   
3. **FliesOutputStream**

   - string的getBytes()方法：将String转为Byte数组
   -  三种write
   - new FileOutputStream(path)是覆盖型，如果new FileOutputStream(path，true)则为追加类型

   - copy代码

   ```java
   		FileInputStream fileInputStream = null;
           FileOutputStream fileOutputStream = null;
   
           String srcpath = "D:\\flie\\code\\hello.txt";
           String despath = "D:\\flie\\code\\helloworld.txt";
   
           try {
               fileInputStream = new FileInputStream(srcpath);
               fileOutputStream = new FileOutputStream(despath,true);
   
               byte[] buf = new byte[8];
               int readLen = 0;
               while((readLen = fileInputStream.read(buf))!= -1)
               {
                   System.out.println(readLen);
                   fileOutputStream.write(buf,0,readLen);
               }
               System.out.println("拷贝成功！");
   
           } catch (IOException e) {
               e.printStackTrace();
           } finally {
               try {
                   if(fileInputStream !=null){
                       fileInputStream.close();
                   }
                   if(fileOutputStream !=null){
                       fileOutputStream.close();
                   }
               } catch (IOException e) {
                   e.printStackTrace();
               }
   
           }
   ```

4. 文件拷贝
5. **FileReader**：读取字节数据，通常用于文本文件
6. **FileWriter**
   - 一定要关闭流！！不然写不进去
   - toCharArray()：转换成字符数组
   - 5种write（翻文档）

7. 节点流：从特定的数据源读写数据

   处理流（包装流）：如BufferedWriter，包装了节点流，消除了不同节点的差异

8. **BufferedWriter和BufferedReader**：字符操作，不要操作二进制文件（如视频、音频、doc等）

9. **BufferedInputStream和BufferedOutputStream**：字节操作，可操作二进制文件87

10. 对象处理流
    - 序列化：保存值和数据类型；反序列化：恢复值和数据类型；
    - 让一个类可序列化：必须实现Serializable接口或者Externalizer接口，一般选择前者，属于是标记接口，没有方法需要实现。
    - **ObjectInputStream(InputStream)：**由readInt()，readBoolean()，readChar()等读取。注意反序列化的顺序必须要和序列化的顺序一致！
    - **ObjectOutputStream(OutputStream)**：由writeInt，writeBoolean，writeChar等写入。
    - 对某个对象（以dog为例）进行序列化时，会将其向上转型为Object类型；将其反序列化时编译类型仍未Object，此时应将其向下转型为dog类型才能正常访问其方法（注意dog类必须在可引用的范围内）。
    - 序列化时static和transient修饰的属性不会被序列化
    - 序列化对象时，对象里的属性也要实现序列化接口
    - 序列化具备可继承性

11. 转换流
    - **InputStreamReader(inputStream, Charset)**：将**字节流包装成字符流**，并**指定处理的编码**
    - OutputStreamWriter(OutputStream, Charset)：将字节流包装成字符流

12. 打印流：printStream, printWriter

    ```java
    PrintStream out = System.out;
    out.print("hello world");//标准输出是在显示器
    out.write("小羊你好".getNytes());//字节流，print底层用的write所以可以直接调用write
    System.setOut(new PrintStream("D:\flie\code\\la.txt"));
    System.out.println("lalala");
    ```

```java
PrintWriter pw = new PrintWriter(new FileWriter("D:\\flie\\code\\lala.txt"))
pw.print("lala");
pw.close();
```

![image-20230720194413614](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231016184704091-260940685.png)



### 639~641 配置文件

1. Properties类：键值对，格式为：键 = 值

2. 常用方法
   - load
   - list 将数据显示到指定设备
   - getProperty(key) 根据键获取值
   - setProperty(key, value) 修改，若没有则添加
   - store 将properties中的键值对存储到配置文件，如果含中文则会存储为unicode码



## 第21章 网络编程（662~688）

### ip地址

- 唯一标识一个主机

- 组成：网络地址 + 主机地址
- ipv4：32位，四字节（每一字节0~255），点分十进制
  - A类（0）、B类（10）、C类（110）、D类（1110）、E类
  - 127.0.0.1表示本机地址
- ipv6：128位，十六字节，16进制 

### 端口

- 用于标识计算机上某个特定的网络程序，两字节

- 尽量不要使用0~1024端口
  - ssh 22，ftp 21，smtp 25，http 80，tomcat 8080，mysql 3306，oracle 1521，sqlserver 1433

### 网络协议

- TCP/IP模型：应用层（Http、ftp、telnet、DNS等），传输层（TCP、UDP等），网络层（IP、ICMP、ARP等），物理+数据链路层（Link）

- TCP协议：传输控制协议

  - 建立连接，释放连接，效率低

  - 三次握手 - > 可靠的
  - 大量数据传输
  - 客户端、服务端

- UDP协议

  - 封装数据包，不用建立连接，不可靠，但速度快
  - 每个数据报大小64k，不适合大量传输

### InetAddress类

```java
//主机名+ip地址
InetAddress localhost = InetAddress.getLocalHost();
System.out.println(localhost);

InetAddress host1 = InetAddress.getByName("Dddd_");
System.out.println(host1);

String hostAddress = host1.getHostAddress();
System.out.println(hostAddress);

String hostName = host1.getHostName();
System.out.println(hostName);
```

```java
Dddd_/192.168.0.105
Dddd_/192.168.0.105
192.168.0.105
Dddd_
```

### socket编程

TCP网络通信编程：注意要先运行服务器端再运行客户端；socket只能获取字节流

- 服务端

```java
//serversocket可以通过accept()返回多个socket实现多并发
        ServerSocket serverSocket = new ServerSocket(9999);
        Socket socket = serverSocket.accept();
        System.out.println("服务端"+socket.getClass());

        InputStream inputStream = socket.getInputStream();
        byte[] buf = new byte[1024];
        int readLen = 0;
        while((readLen = inputStream.read(buf))!=-1)
        {
            System.out.println(new String(buf,0,readLen));
        }
        inputStream.close();
        socket.close();
        serverSocket.close();
```

- 客户端

```java
 //连接服务端（ip，端口）
        Socket socket = new Socket(InetAddress.getLocalHost(),9999);
        System.out.println("客户端"+socket);
        //得到和socket相关联的输出流对象
        OutputStream outputStream = socket.getOutputStream();
        outputStream.write("hello,server".getBytes());
        //必须关闭流对象和socket
        outputStream.close();
        socket.close();
```

- 设置结束标记

```java
socket.shutdownOutput();
socket.shutdownInput();
```

- 字节流

```java
BufferedWriter bufferedWriter = new BufferedWriter(new OutputStreamWriter(outputStream));

bufferedWriter.write("hello,server");
bufferedWriter.flush(); //需要手动刷新，否则不会写入
writer.newLine();//对方需要使用readLine读取
```

### netstat指令

- netstat -an 查看当前主机网络情况，包括端口监听和网络连接
- netstat -an |more 可以分页显示

### UDP网络通信编程

1. UDP原理

- 数据报

- 没有明确的服务端和客户端，变为**接收端**和**发送端**
- 接受和发送数据通过DatagramSocket完成
- 将数据封装到DatagramPacket对象（**字节数组**），接收需要拆包

2. 代码部分

```java
DatagramSocket socket = new DatagramSocket(9999);
//一个数据包最大64k
byte[] buf = new byte[64*1024];
DatagramPacket packet = new DatagramPacket(buf, buf.length);
//若没有数据报则会阻塞
socket.receive(packet);

int length = packet.getLength();
byte[] data = packet.getData();

String s = new String(data, 0, length);
System.out.println(s);

socket.close();
```

```java
DatagramSocket socket = new DatagramSocket(9998);
byte[] data = "hello udpA~".getBytes();
DatagramPacket packet = new DatagramPacket(data, data.length, InetAddress.getByName("192.168.0.105"), 9999);
socket.send(packet);
socket.close();
```



## 第23章 反射（711~730）看到725

### 反射入门

- 使用反射机制读取properties文件内容

  ```java
  Properties properties = new Properties();
  properties.load(new FileInputStream("src\\re.properties"));
  String classfullpath = properties.get("classfullpath").toString();"com.hsp.cat"
  String methodName = properties.get("method").toString;//"hi"    
  
  Class cls = Class.forName(classfullpath);
  Object o = cls.newInstance();
  Method method1 = cls.getMethod(methodName);
  //传统是对象.方法()，反射机制是 方法.invoke(对象)
  method1.invoke(o);
  ```

- 反射相关的主要类
  - java.lang.Class
  - java.lang.reflect.Method
  - java.lang.reflect.Field
  - java.lang.reflect.Constructor
- 反射动态性地体现：http://t.csdn.cn/jOVtE

### Class类

- class类常用方法

  ```java
  String classALLPath = "com.hspedu.Car";
  Class<?> cls = Class.forName(classALLPath);
  System.out.println(cls);//com.hspedu.Car
  System.out.println(cls.getClass());//java.lang.Class
  System.out.println(cls.getPackage().getName());//包名
  //通过cls生成对象实例
  Car car = (Car)cls.newInstance();
  System.out.println(car);//car.toString()
  //通过反射获取属性brand
  Field brand = cls.getField("brand");
  System.out.println(brand.get(car));
  //得到所有属性
  Field[] fields = cls.getFields();
  for(Field f : fields){
  	System.out.println(f.getName());
  }
  ```

- 获取class对象的方式
  - Class.forName()：已知类的全类名，且该类在类路径下；多用于配置文件
  - 类加载器得到Class对象
  - 类.class：已知具体的类；最安全可靠；多用于参数传递（如通过反射得到对应的构造器对象
  - 基本数据类型对应的包装类.TYPE
  - 对象.getClass()

### 类加载

- 动态加载（反射）和静态加载（依赖性强）

- 类加载的过程
  - java源码 cat.java 
  - （javac编译）字节码文件 cat.class
  - 加载 Loading
  - 连接 Linking 验证、准备、解析
  - 初始化 执行静态代码块等

### 获取类结构信息





## 第25章 JDBC和数据库连接池（821-857）没学完

### 驱动类 接口

- jar包

### JDBC编写步骤

- 前置工作：在项目下创建一个文件夹比如libs
- 将mysql.jar 拷贝到该目录下，点击add to project...加入到项目中

```java
//注册驱动
Driver driver = new Driver();
//得到连接
String url = "jdbc:mysql://localhost:3306/xiaoyang";
Properties properties = new Properties();
properties.setProperty("user","root");//用户
properties.setProperty("password","1234");//密码
Connection connect = driver.connect(url, properties);
//执行sql
String sql = "insert into student values (1, 'lihua')";
Statement statement = connect.createStatement();
int rows = statement.executeUpdate(sql);//dml语句返回的是受影响的行数
//关闭资源
statement.close();
connect.close();      
```





## 第27章 正则表达式 878~904

- 正则实现

  ```java
  String content = "124248923jdsfa242";
  //在java的正则表达式中，\\表示特殊符号的转义符
  String regStr = "\\d\\d\\d\\d";
  //创建模式对象
  Pattern pattern = Pattern.compile(regStr);
  //创建匹配器
  Matcher matcher = pattern.matcher(content);
  //开始匹配
  while(matcher.find())
  {
      System.out.println(matcher.group(0));
  }
  ```

- 分组
- ![image-20230811144512172](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231016184703573-857834346.png)

- ![image-20230811152355262](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231016184702881-1786902505.png)


- 限定符

  ![image-20230811154544317](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231016184702201-932585805.png)

![image-20230811154743847](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231016184701641-587879800.png)

- 定位符

  ![image-20230811154926076](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231016184701010-388682626.png)

- 命名分组

  ```java
  String content = "124248923jdsfa242";
  //在java的正则表达式中，\\表示特殊符号的转义符
  String regStr = "(?<g1>\\d\\d)\\d\\d";
  //创建模式对象
  Pattern pattern = Pattern.compile(regStr);
  //创建匹配器
  Matcher matcher = pattern.matcher(content);
  //开始匹配
  while(matcher.find())
  {
      System.out.println(matcher.group(0));
      //下面这两句是一个意思
      System.out.println(matcher.group(0));
      System.out.println(matcher.group("g1"));
  }
  ```

- ![image-20230811164456590](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231016184700220-1800337659.png)

- 正则表达式元字符详细说明见韩顺平资料
- **反向引用**（如找abba型）
  - 





