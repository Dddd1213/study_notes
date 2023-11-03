# Docker

[TOC]

# 1

# 安装

- 参考：[⁤⁤‌‍⁢⁡⁤‬⁡‬‌﻿⁡⁢⁣‬⁡‌⁣‌﻿⁤⁡‌⁣‍⁢⁤⁣⁣⁢‍‍⁡⁣⁣‬‍﻿⁢⁣‌⁡Linux环境搭建 - 飞书云文档 (feishu.cn)](https://b11et3un53m.feishu.cn/wiki/FJAnwOhpIihMkLkOKQocdWZ7nUc)
- 密码：密码：j.N?-+4[
- （这个也是黑马视频对应的详细笔记）
- docker -v
  - 检查是否安装好
  - 版本： 24.0.7
- docker images
  - 检查是否安装好且启动

## 镜像和容器

- 镜像：应用+应用所需的环境，配置，系统函数库
- 容器：docker运行时创建隔离环境，避免相互影响
- 镜像仓库：hub.docker.com

## 命令解读

```
docker run -d \
  --name mysql \
  -p 3306:3306 \
  -e TZ=Asia/Shanghai \
  -e MYSQL_ROOT_PASSWORD=123 \
  mysql
```

- `docker run -d` ：创建并运行一个容器，`-d`则是让容器以后台进程运行
- `--name mysql ` : 给容器起个名字叫`mysql`，你可以叫别的
- `-p 3306:3306` : 设置端口映射。
  - **容器是隔离环境**，外界不可访问。但是可以将**宿主机端口**映射到**容器内端口**，当访问宿主机指定端口时，就是在访问容器内的端口了。
  - 容器内端口往往是由容器内的进程决定，例如MySQL进程默认端口是3306，因此容器内端口一定是3306；而宿主机端口则可以任意指定，一般与容器内保持一致。
  - 格式： `-p 宿主机端口:容器内端口`，示例中就是将宿主机的3306映射到容器内的3306端口
- `-e TZ=Asia/Shanghai` : 配置容器内进程运行时的一些参数
  - 格式：`-e KEY=VALUE`，KEY和VALUE都由容器内进程决定
  - 案例中，`TZ=Asia/Shanghai`是设置时区；`MYSQL_ROOT_PASSWORD=123`是设置MySQL默认密码
- `mysql` : 设置**镜像**名称，Docker会根据这个名字搜索并下载镜像
  - 格式：`REPOSITORY:TAG`，例如`mysql:8.0`，其中`REPOSITORY`可以理解为镜像名，`TAG`是版本号
  - 在未指定`TAG`的情况下，默认是最新版本，也就是`mysql:latest`

# 2

# docker基础

## 常见命令

- docker exec -it nginx bash
  - 连接已启动的nginx容器
- docker run -d --name nginx -p 80:80 nginx
- docker load -i jdk.tar

![image-20231027221848893](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231029211152134-1592856950.png)

![image-20231027221810063](https://img2023.cnblogs.com/blog/3299940/202310/3299940-20231029211150091-905142347.png)

## 命令别名

```
# 修改/root/.bashrc文件
vi /root/.bashrc
内容如下：
# .bashrc

# User specific aliases and functions

alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'
alias dps='docker ps --format "table {{.ID}}\t{{.Image}}\t{{.Ports}}\t{{.Status}}\t{{.Names}}"'
alias dis='docker images'

# Source global definitions
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi

#使命令生效
source /root/.bashrc
```



## 数据卷

- 本来修改：需要进入容器，然后修改
- 问题：镜像只提供最小化的系统环境，提供当前应用对应镜像必备的系统函数，如什么vi，ll等操作都没有，无法在里面修改文件等
- 解决：挂载数据卷，直接在宿主机的对应文件上修改即可
- 数据卷：volume，虚拟目录，容器内目录与宿主机目录之间映射的桥梁
- 位置：/var/lib/docker/volumes
- 命令
  - docker volume create 创建
  - docker volume ls 查看
  - docker volume rm 删除
  - docker volume inspect 数据卷名 查看某个数据卷详情
  - docker volume prune 清除数据卷
  - docker run -v 数据卷:容器内目录 注意必须要在创建容器时！！（数据卷的名字可以自己取）

- docker inspect 容器名
  - 查看容器详情，如数据卷挂载信息等

### 本地目录挂载

- 容器会自动生成匿名卷
- 缺点：名称复杂，不好找，删容器后没同步
- 解决：挂载到本地目录下
-  -v 本地目录:容器内目录

- （挂载时不知道文件位置都可以去hub.docker.com里查官方镜像文档



## 镜像

- 结构：分层
  - 一些基础部分可以复用
  - 最底层：基础镜像
  - 最顶层：入口

### Dockerfile

- |    **指令**    |                   **说明**                   |
  | :------------: | :------------------------------------------: |
  |    **FROM**    |                 指定基础镜像                 |
  |    **ENV**     |        设置环境变量，可在后面指令使用        |
  |    **COPY**    |         拷贝本地文件到镜像的指定目录         |
  |    **RUN**     |  执行Linux的shell命令，一般是安装过程的命令  |
  |   **EXPOSE**   | 指定容器运行时监听的端口，是给镜像使用者看的 |
  | **ENTRYPOINT** |     镜像中应用的启动命令，容器运行时调用     |

- 官方文档：https://docs.docker.com/engine/reference/builder/

- docker build -t myImage:1.0 .
  - -t 表示给镜像取名
  - myImage 镜像名
  - . 指定dockerfile所在的目录，当前目录就为.



## docker网络

- 问题：docker0分的ip地址，如果关掉再重启可能会变化
- 解决：自定义网络容器，可以通过**容器名**互联

|         **命令**          |         **说明**         |
| :-----------------------: | :----------------------: |
|   docker network create   |       创建一个网络       |
|     docker network ls     |       查看所有网络       |
|     docker network rm     |       删除指定网络       |
|   docker network prune    |     清除未使用的网络     |
|  docker network connect   | 使指定容器连接加入某网络 |
| docker network disconnect | 使指定容器连接离开某网络 |
|  docker network inspect   |     查看网络详细信息     |

- 创建的时候加--network 网络容器名，可以在创建容器的时候连接，且不会再连接默认网桥



# 3

# 项目部署

- Maven中 package（跳过单元测试），然后将jar放到虚拟机中，构建镜像，创建并运行容器

- 容器名互联 记得要在一个网络！

## DockerCompose

- 用一个单独的 docker-compose.yml 模板文件（YAML 格式）来定义一组相关联的应用容器。

- 官方文档：[Compose file version 3 reference | Docker Docs](https://docs.docker.com/compose/compose-file/compose-file-v3/)
- （镜像和网络都能帮你创建！）

```yml
version: "3.8"

services:
  mysql:
    image: mysql
    container_name: mysql
    ports:
      - "3306:3306"
    environment:
      TZ: Asia/Shanghai
      MYSQL_ROOT_PASSWORD: 123
    volumes:
      - "./mysql/conf:/etc/mysql/conf.d"
      - "./mysql/data:/var/lib/mysql"
      - "./mysql/init:/docker-entrypoint-initdb.d"
    networks:
      - hm-net
  hmall:
    build: 
      context: .
      dockerfile: Dockerfile
    container_name: hmall
    ports:
      - "8080:8080"
    networks:
      - hm-net
    depends_on:
      - mysql
  nginx:
    image: nginx
    container_name: nginx
    ports:
      - "18080:18080"
      - "18081:18081"
    volumes:
      - "./nginx/nginx.conf:/etc/nginx/nginx.conf"
      - "./nginx/html:/usr/share/nginx/html"
    depends_on:
      - hmall
    networks:
      - hm-net
networks:
  hm-net:
    name: hmall
```

- 命令：docker compose [OPTIONS] [COMMAND]
  - 表格里前俩是options

| **参数或指令** |                           **说明**                           |
| :------------: | :----------------------------------------------------------: |
|       -f       |                 指定compose文件的路径和名称                  |
|       -p       | 指定project名称。project就是当前compose文件中设置的多个service的集合，是逻辑概念 |
|       up       |                  创建并启动所有service容器                   |
|      down      |                   停止并移除所有容器、网络                   |
|       ps       |                      列出所有启动的容器                      |
|      logs      |                      查看指定容器的日志                      |
|      stop      |                           停止容器                           |
|     start      |                           启动容器                           |
|    restart     |                           重启容器                           |
|      top       |                        查看运行的进程                        |
|      exec      |                 在指定的运行中容器中执行命令                 |





# 报错合集

## bash: ping: command not found

- 描述：把mysql和redis放在同一个network，然后尝试两边能不能ping通时出现
- 原因：当前环境下载的基础镜像只包含简单的操作系统，相当于裸机状态，要想用别的命令需要手动安装。
- 解决：
  - apt-get update
  - apt install iputils-ping
  - apt install net-tools



## 退出mysql容器时出现There are stopped jobs.且无法退出

- 原因：按到Ctrl Z挂起了一些命令
- 解决：
  - jobs -l 查看详细列表
  - fg %1 重新启动某程序（1是详细列表中中括号的数字）
  - kill %1 直接杀死  











































