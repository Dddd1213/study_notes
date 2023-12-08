# 后台管理系统

[TOC]



## 整体结构

- modules：依赖
- public：静态文件
- assets：图片，静态文件
- components：主键
- router：路由
- store：状态管理器
- views：放页面
- app.view：入口页面
- main.js：入口



## 登录步骤

### 1.1 安装Element-ui组件并测试

- npm run serve 启动

#### 报错：Uncaught TypeError: Cannot read properties of undefined (reading 'prototype')

- 原因：vue3和Element ui不匹配
- 解决：在vue ui里创项目的时候设置一下。。
- （弄了一上午了vue我恨你）

![image-20231017111523868](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231017111523868.png)



### 1.2 安装插件

- axios：基于promise的http库，类ajax
- qs
- mockjs：生成随机数据



### 1.3 登录页面开发

- layout布局：查文档



### 问题：第7节，通过设置height无法让颜色铺满全屏，后面align-items: center;也无效

- 好像是因为设了flex，父元素高度缺省，所以子元素设高度没用
- 解决：把100%改成100vh，vh指当前屏幕的高度
- 另外：还试了一些比如设置父元素高度等方式但是这里没用
- （又弄一下午vue我更恨你了）



### 1.4 登录交互

1. 禁用session（前后端分离）

2. 后端生成验证码+随机码，验证码为value保存到redis中，然后把随机码验证码图片的Base64字符串码发送到前端

3. 前端提交登录表单（用户验证码，用户名，密码，**随机码**）

4. 后端校验，成功在header里返回Jwt
5. 前端把它保存在localstorange（浏览器中存储）中

### 问题：模拟验证码图片不显示

![image-20231017201947633](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231017201947633.png)

- 这个地方需要用小写的x而不是乘号！！！！！！！
- 另外注意：rtype那个地方不能用大写字母，不然也会报错！！！！



### 1.5 axios全局拦截器

- axios.js

#### 页面报错：at handleError (webpack-internal:///./node_modules/webpack-dev-server/client/overlay.js:299:58)     at eval (webpack-internal:///./node_modules/webpack-dev-server/client/overlay.js:322:7)

- 问题：和视频不一样，这个报错出现了一整页红色页面，不知道是否正确

- 原因：是webpack搞出来的！

- 排查方式：打开控制台能看到这个框的一些属性

- 解决：在配置文件加一下就行

  ```js
  const { defineConfig } = require('@vue/cli-service')
  module.exports = defineConfig({
    transpileDependencies: true,
    devServer: {
      client: {
        overlay: false,
      },
    }
  })
  ```

- （这个问题真是走了好多弯路，刚开始以为是版本又不匹配导致的问题，改了半天）
- [vue-cli 关闭 Uncaught error 的全屏提示_田八的博客-CSDN博客](https://blog.csdn.net/qq_33733799/article/details/130681501)



## 后台

### 2.1 后台布局

- 整体布局：container布局容器
- 导航菜单：导航

### 2.2 每页都有的相同布局

- 抽取sideMenu：每个页面都有的相同的代码，然后引用

  ```
  <SideMenu></SideMenu>
  
  <script>
    import SideMenu from "@/views/inc/SideMenu";
    export default {
      name: "Index",
      components:{
        SideMenu
      }
  
  }
  ```

- 另：子路由

  - 把Home做为父级路由，index作为子路由
  - 在home.vue里加router-view

### 2.3 导航栏

1. 新增路由
2. 菜单绑定路由
   - <router-link to="/index"\>包围就行

3. 个人中心
   - 跳转链接：<router-link :to="{name: 'UserCenter'}"\>个人中心</router-link\>
   - 然后写usercenter的路由和页面
4. 登出
   - <el-dropdown-item @click.native="logout">退出</el-dropdown-item\>
   - 然后再写logout方法
   - 注意：登出后要清除store里的信息



### 2.4 动态导航与路由绑定

- 把写死的数据简化成一个json数组数据，然后for循环展示出来
- computed动态监控
- （没太懂）



### 2.5 动态标签页与导航绑定

- 不重复新增：前面加个判断editableTabs里面有没有



## 菜单

- 新增：对话框弹窗

- （不知道为什么我的所有东西都自带居中...）

- 删除：气泡确认框

- 点击按钮绑定事件

  - @click

  ```
  <el-button type="primary" @click="submitForm('editForm')">立即创建</el-button>
  ```

  - 然后在method里写一个事件

  ```js
  submitForm(formName) {
    this.$refs[formName].validate((valid) => {
      if (valid) {
        this.$axios.post('/sys/menu/' + (this.editForm.id?'update' : 'save'), this.editForm)
            .then(res => {
  
              this.$message({
                showClose: true,
                message: '恭喜你，操作成功',
                type: 'success',
                onClose:() => {
                    //更新数据
                  this.getMenuTree()
                }
              });
  
              this.dialogVisible = false
            })
      } else {
        console.log('error submit!!');
        return false;
      }
    });
  },
  ```



## 角色管理

- 批量删除：把id放到数组里面
  - 通过勾选时产生的multipleSelection中的数据，获得要删除的id数组

```
delHandle(id) {

  var ids = []

  if (id) {
    ids.push(id)
  } else {
    this.multipleSelection.forEach(row => {
      ids.push(row.id)
    })
  }

  console.log(ids)

  this.$axios.post("/sys/role/delete", ids).then(res => {
    this.$message({
      showClose: true,
      message: '恭喜你，操作成功',
      type: 'success',
      onClose:() => {
        this.getRoleList()
      }
    });
  })
},
```

- 勾选数据后激活批量删除按钮

```
handleSelectionChange(val) {
  console.log("勾选")
  console.log(val)
  this.multipleSelection = val;
//通过看var里的数据等不等于0
  this.delBtlStatu = val.length == 0
},
```

- 分页

- 分配权限：一个用户拥有一个角色，不同角色可以对菜单进行不同操作（不同权限），可以给用户分配操作权限

## 用户管理



# 后端！！

- devtools：热部署插件，不用每次修改都手动重启

## MP



## 数据库

- 测试遇到接二连三报错，就是启动不起来，结果还是版本问题
  - 记录一下现用版本：springboot 2.7.5，java8
- 序列化：[Java中的实体类为什么要 implements Serializable?_实体类继承serializable-CSDN博客](https://blog.csdn.net/weixin_45410366/article/details/126618512?ops_request_misc=%7B%22request%5Fid%22%3A%22169812666816800225523227%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=169812666816800225523227&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-126618512-null-null.142^v96^pc_search_result_base2&utm_term=implements Serializable&spm=1018.2226.3001.4187)



## 结果封装

- 是否成功
- 结果消息
- 结果数据



## Security

![image-20231024143931955](C:\Users\31067\AppData\Roaming\Typora\typora-user-images\image-20231024143931955.png)

- 验证码Filter，认证成功，认证失败

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

- 访问/test时会跳一个登录页面，信息保存在cookie

## Redis

- 工具类
- 配置文件：序列化方式



## 用户认证

### 验证码

- 生成code
- 用code生成图片传给前端显示
- 将code在redis中存一份，设置过期时间，用于用户输入验证码后验证

- （debug又5小时）

### 跨域问题

- CorsConfig（比较固定，直接复制的）
- SecurityConfig

### Security类

- 认证成功/失败处理器
- （todo：这个和返回全局异常类及返回result有什么区别，把result一json形式传过去吗？）
- login默认返回的是一个页面，这里需要定义成功失败的返回类型为json



## Jwt

- （todo：为什么有的异常当时throw，有的trycatch返回null然后在后面根据null处理，有的用handler有的Result.fail(mess)？？）

  

## 权限问题

- 通过Security内置的权限注解进行权限管理
  - @PreAuthrize("hasRole('admin')")
  - ...
- 用户登录：调用UserDetailsService.loadUserByUsername，返回userdetails时会用到权限
- JwtAuthenticationFilter中把token的信息给SecurityContextHolder时，token里携带权限

## 添加缓存

- 由于每次进行操作都需要进行权限获取，对authority获取频繁，（SysUserServiceImpl中getUserAuthorityInfo），将authority放到redis里，"GrantedAuthority:" + sysUser.getUsername()作为key
- 下次执行该方法时，先判断缓存中有没有
- 需要清除缓存的情况
  - 给该用户分配权限
  - 角色编码发生改变
  - 菜单权限编码发生改变



## 退出







官方笔记：https://www.markerhub.com/course/blogs/2


