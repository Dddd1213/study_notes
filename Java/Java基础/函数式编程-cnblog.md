# 函数式编程

[TOC]

## Lambda表达式

- 基于匿名内部类的简化
- 参数需要实现一个接口，接口中只有唯一一个方法
- 只关注方法的参数和方法体

### 省略规则

- 类名可省略
- 参数类型可省略
- 方法体只有一句代码时，大括号、return、这代码的分号可以省略
- 方法只有一个参数时小括号可以省略
- （可以先写匿名内部类然后alt+enter）

## stream流

- 需要加终结操作，才会生效

### 创建流

- 创建流
  - map.entrySet返回一个set集合（转换成单列集合），集合中的买个元素都是一个Entry对象（有点像pair）

![image-20231123122410357](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231126001556726-752575448.png)

### 中间操作

- filter：按条件过滤
- map：把流当中的元素进行计算或转换
  - 参数中的第一个泛型和流本身的元素类型应该一致
- distinct：去重
  - 底层是Object中的equals，本身是判断对象的地址是否相同
  - 如果要判断属性相同，则需要重写
  - 可以在实体类上加@EqualsAndHashCode，就能达到重写的目的，使属性值相同即equal
- sorted：
  - 实体类需要实现Comparable接口，然后在实现类中写比较的方式
  - 或者：实现一个匿名内部类接口
- limit：可以限制流的最大长度，多的丢掉
- skip：跳过前n个元素
- flatMap：把一个对象转换成多个对象放到流中



### 终结操作

- forEach ：对流中的元素进行遍历
- count
- max
- min
- collect：把流转换成集合

![image-20231123142554508](https://img2023.cnblogs.com/blog/3299940/202311/3299940-20231126001556101-355999784.png)

- 查找与匹配：前三个返回bool
  - anyMatch
  - allMatch
  - noneMatch
  - findAny：获取任意一个元素
    - 返回类型：Optinal<T\>
    - 里面有个ifPresent方法，如果存在就返回，可防止空指针异常
  - findFirst
- reduce：归并，对流中的数据照指定的方式计算出一个结果（缩减操作）
  - 第一个参数：设置初始化值；第二个参数：写计算的方式



## Optional

- 类似一个包装类，可以把对象封装进去，然后用包装类的方法优雅地避免空指针异常

- **ofNullOptional**

  ```java
  Author author = new Author();
  Optional<Object> objectOptional = Optional.ofNullable(author);
  ```

- **ifPresent**：安全消费
- orElseGet：设置默认值，如果为空就取到默认值
- orElseThrow：如果为空就根据传进来的参数创建异常抛出
- filter：过滤
- isPresent：是否存在
- map：和stream里的差不多



## 函数式接口

- 接口中有且只有一个抽象方法



## 方法引用

- 方法体只有一个方法的调用
- 格式：
  - 类名或对象名::方法名



## 并行流

- .parallel：转换成并行流



课程：[Lambda表达式&Stream流-函数式编程-java8函数式编程(Lambda表达式，Optional，Stream流)从入门到精通-最通俗易懂_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1Gh41187uR/?spm_id_from=333.337.search-card.all.click)