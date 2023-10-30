# DAY 1 

[TOC]

## 一、虚拟机及Cenos安装

- b站韩顺平 [006_韩顺平Linux_CentOS7.6安装_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1Sv411r7vd?p=6)

- 随机密码生成网：[生成随机密码 - 密码生成器 - 密码批量生成器 (bmcx.com)](https://suijimimashengcheng.bmcx.com/)



## 二、重置密码

​       ~~不知道该说些什么好...设密码的时候复制粘贴了两次不知道粘上去了些什么东西、、~~

- 重置密码：[(322条消息) 【CentOS】虚拟机忘记密码，如何重置密码。_centos重置密码_猫巳的博客-CSDN博客](https://blog.csdn.net/qq_31635851/article/details/124799296?ops_request_misc=&request_id=&biz_id=102&utm_term=虚拟机cenos忘记密码&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-124799296.142^v86^insert_down38v5,239^v2^insert_chatgpt&spm=1018.2226.3001.4187)

- 注意：重置密码输入新密码的时候页面上是不显示的



## 三、网络连接的三种模式

- 桥接模式：都在同一个网段，虚拟系统可以和外部系统通讯，但容易造成ip冲突。
- NAT模式：主机上生成对应的虚拟的网卡，虚拟机和外部系统不在一个网段，但可以通过主机ip代理和外部通讯，且不易造成ip冲突，但外部传不进来。 
- 主机模式：独立的系统。



## 四、虚拟机克隆

已经安装了一台linux，还需要更多：

1. 直接copy一份安装好的虚拟机文件
2. vmware克隆操作（克隆时需要先关闭linux）

参考：b站韩顺平 [008_韩顺平Linux_虚拟机克隆_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1Sv411r7vd?p=8)



## 五、Linux 目录结构

- 在linux世界里，一切皆文件！
- 主要是下图



- 常用
  - /bin (/user/bin、/user/local/bin)  经常使用的命令
  - /sbin  系统管理员使用的系统管理程序
  - /home  存放普通用户的主目录
  - /root  系统管理员
  - /etc  所有系统管理需要的配置文件和子目录
  - /usr  **重要**！存放用户的很多应用程序和文件（类似windows的program files）
  - /boot  存放启用linux的核心文件，包括一些连接文件和镜像文件
  - /media linux系统会自动识别一些设备，例如U盘、光驱等，识别后会把识别的设备挂在该目录下
  - /mnt 让用户临时挂载别的文件系统



# Day 2 

# 一、远程登录

- 远程登录 Linux-Xshell

  [Free for Home/School - Xshell and Xftp Free Licensing (netsarang.com)](https://www.netsarang.com/en/free-for-home-school/)

- 远程上传下载文件 Xftp

  - 乱码：左上角->文件->当前会话属性->选项->编码，换成utf-8

- ifconfig  查看ip（虚拟机里Linux的ip不是一直固定的）

  ping  测试两个ip是否互通

  

## 二、vi和vim编辑器

- 正常模式/一般模式
- 插入模式：按下“i, I, o, O, a, A, r, R”等任意字符进入
- 命令行模式：先输入esc表示退出，再输入“：”进入
  - wq  保存并退出
  - q  退出
  - q!  强制退出且不保存
- 快捷键
  - 【正常模式】拷贝当前行 ”**yy**“，拷贝当前行向下5行 “5yy”，粘贴 “p”。
  - 【正常模式】删除当前行 “**dd**”，删除当前行向下5行 ”5dd“。
  - 【命令行模式】在文件中查找某个单词 输入“**/关键字**”，回车查找，输入“n”查找下一个，再次输入“/” 即可输入新的关键词查找。
  - 【命令行模式】文件行号：输入“**set nu**”显示，“set nonu”不显示。
  - 【正常模式】“**G**”定位到最末行，“**gg**”定位到文档最首行。
  - 【正常模式】“**u**”撤销（相当于windows的“ctrl+z”）。
  - 【正常模式】将光标移动到第x行，先输入 x，再输 **shift+g**。



## 三、关机重启、登录注销

- **关机&重启**

  - shutdown -h now/1  现在/1分钟后 关机
  - shutdown -r  重启
  - halt  关机
  - reboot  重启
  - sync  内存数据同步到磁盘（防止内存数据丢失）

- **登录注销**

  - 建议先用普通用户登录，然后“su -用户名”命令来切换管理员身份
  - logout 登出 （在图形运行级别无效，在运行级别3下有效）

- #表示超级用户，也就是root用户

  $表示普通用户，权限较低

- hostname 查看主机名 修改主机名



## 四、账号管理

- 添加用户  useradd 选项 用户名
  - -c comment  制定一段注释性描述
  - -d  指定用户主目录
  - -g  指定用户所属的用户组
  - -G   指定用户所属的附加组
  - -m  使用者目录不存在则自动建立
  - -s  指定用户登录的Shell
  - -u  指定用户号
  - passwd 用户名  给该用户设置密码 【root】
  - passwd 修改密码

- 删除用户 userdel 选项 用户名

​	eg. userdel -r aaa  删除用户aaa并将目录页一并删除（-r）

- 修改用户 usermod 选项 用户名

- 锁定账户
  -  passwd -l 用户名
  -  passwd -d 用户名  将密码清空

​		上面两个都能锁定，锁定之后就无法登录了



## 五、用户组管理

- 创建一个用户组 

  - groupadd abc  创建用户组abc
  - -g 520  制定id号为520 如果不指定就是自增1

- 删除用户组  groupdel

- 更改用户组 groupmod

  - -g  修改id
  - -n  修改名字

- 切换用户组

  - 登录当前用户
  - $ newgrp root

- 文件的查看

  - cat /etc/passwd  查看配置文件

    用户名：口令（登录密码，我们不可见）：用户标识号：组标识号：注释性描述：主目录：登录shell

  - cat /etc/group 用户组信息

  - cat /etc/shadow 可以看加密后的用户口令




# Day 3

## 一、磁盘管理

- 命令

  - df  列出文件系统整体的磁盘使用量
    - -h 可以把数字转换成以M为单位

  - du  检查当前磁盘空间使用量

    - -a  可以看到隐藏的文件和子集目录

    - -sm /* 检查每个目录所占用的容量

- MAC 或者用Linux挂载本地磁盘或文件（一般挂载在mnt目录下）

  - 挂载：mount 外部设备 目录  
  - 强制卸载：umount -f  挂载位置



## 二、进程管理

- 基本概念

  - 每一个进程都有一个父进程
  - 一般服务后台运行，基本程序前台运行

- 命令

  - ps  查看当前系统中正在执行的进程信息

    - -a  查看当前终端运行的所有进程

    - -u  以用户的信息显示进程

    - -x  显示后台运行进程的参数

    - -aux  查看所有的进程

    - grep  查找文件中符合条件的字符串

    - |  管道符，A|B表示把A的结果作为输出操作B

      eg.  ps -aux|grep mysql  查找和myqsl相关的进程

    - -ef  查看父进程的信息

  - pstree 进程树

    - -p  显示父id
    - -u  显示用户组

  - kill -9 id  结束进程



## 三、环境安装

### 环境安装的三种方式

- rpm
- 解压缩
- yum在线安装

# DAY 4

## 文件目录指令

- pwd：显示当前工作目录的绝对路径

- ls [选项] [目录或文件]
  - -a  显示当前目录的所有文件和目录，包括隐藏的
  - -l  以列表方式显示所有信息
- cd [参数]：切换到指定目录
  - cd ~ 或者cd ：回到当前用户的家目录
  - cd.. ：回到上一级目录
- mkdir [选项] 要创建的目录：创建目录
  - -p  创建多级目录
- rmdir [选项] 要删除的目录：删除**空目录**
  - rm -rf 要删除的目录：删除非空目录

- touch：创建一个空文件
- cp [选项] source dest
  - -r  递归复制整个文件夹
- mv 原文件名 新文件名





## crond任务调度 

- 任务调度：系统在某个时间执行特定命令或程序
- 基本语法：crontab [选项]
- 常用选项
  - -e  编辑crontab定时任务
  - -l  查询crontab任务
  - -r  删除当前用户所有的crontab任务



# Day 5

## 环境安装：rpm yum 解压缩

## 一、rpm的介绍和使用

- 介绍：redhat package manager，类似windows的setup.exe，是一种用于互联网下载包的打包安装工具。
- 查询命令
  - rpm -qa|grep xx：查询已安装的xx的rpm列表
  - rpm -a 软件包名：查询软件包是否安装
  - rpm -qi 软件包名：查询软件包中信息
  - rpm -ql 软件包名：查询软件包中的文件
  - rpm -qf 文件全路径名：查询文件所属的软件包
- 安装卸载命令
  - rpm -ivh rpm包的全路径名称：安装
  - rpm -e 软件包名：卸载该软件包



## 二、yum的介绍和使用

- 介绍：shell前端软件包管理器，基于rmp包管理，能够从制定的服务器自动下载rpm包并安装，**可以自动处理依赖性关系**，并且一次性安装所有依赖的软件包。
- 基本指令：
  - yum list | grep xx软件列表：查询yum服务器是否有需要安装的软件
  - yum instal xxx下载安装：安装指定的yum包



## 三、解压缩

- 以jdk为例，参考：[086_韩顺平Linux_安装配置JDK8_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1Sv411r7vd?p=86&vd_source=986015d7427e83da4b4f898ee289dd8b)

































