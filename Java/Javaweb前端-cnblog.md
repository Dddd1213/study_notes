# HTML+ CSS+ JS+ Vue+ Element

[TOC]

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

  -  parse()：将JSON字符串转化为JS对象

  -  stringify()：JS对象转化为JSON字符串

**4.5 BOM（对象）**

-  **window 浏览器窗口对象**
  - 属性：History，Location，navigator
  - 方法：alert()，confirm()，setInterval()，setTimeout()
- navigator 浏览器对象
  - href 设置或返回完整的url
- Screen 屏幕对象
- History 历史记录对象
- Location 地址栏对象

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











参考：[黑马程序员2023新版JavaWeb开发教程，实现javaweb企业开发全流程（涵盖Spring+MyBatis+SpringMVC+SpringBoot等）_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1m84y1w7Tb/)































