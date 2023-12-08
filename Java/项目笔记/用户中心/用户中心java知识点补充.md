# 用户中心Java知识点补充

[TOC]

## ==和equals的区别

- “==”是运算符，如果是基本数据类型，则比较**存储的值**；如果是引用数据类型，则比较**所指向对象的地址值**（是否是同一个对象）。
- equals是Object的方法，比较的是**所指向的对象的地址值**，一般情况下，重写之后比较的是**对象的值**（如String）。

- 基本数据类型和引用数据类型：[Java数据类型：基本数据类型和引用数据类型_java基本数据类型和引用数据类型_hellosc01的博客-CSDN博客](https://blog.csdn.net/sc179/article/details/107991323?ops_request_misc=%7B%22request%5Fid%22%3A%22169175836916800182189574%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=169175836916800182189574&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-107991323-null-null.142^v92^chatgptT3_1&utm_term=java中基本数据类型和引用数据类型&spm=1018.2226.3001.4187)