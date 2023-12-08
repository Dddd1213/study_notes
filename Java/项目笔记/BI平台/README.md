# 数悦AI数据分析平台

> 作者：羊毛

## 项目介绍

- 本项目是一款智能数据分析平台，用户只需导入原始数据集和分析需求，即可自动生成可视化图表和分析结论，从而降低数据分析的人工成本、提高数据分析效率。
- 技术选型：SrpingBoot + AIGC + Redis + MongoDB + RabbitMQ 等

## 项目功能

- 用户登录、登出、注册等
- ip限流：限制单用户一定时间的访问次数
- 智能分析（异步）：用户上传excel数据表，即可获得AI分析结论和图表展示
- 实时推送：生成图表结束后会向用户推送信息

## 快速启动

- 下载/拉取本项目到本地
- 通过 IDEA 代码编辑器进行打开项目，等待依赖的下载
- 修改配置文件 `application.yaml` 的信息，比如数据库、Redis、RabbitMQ等

## 环境配置（建议）

- Java Version：1.8.0

- MySQL：8.0.29

- Redis：6.2.14

- RabbitMQ：3.8

## 项目架构图

```
 ├─main
 │  ├─java
 │  │  └─com
 │  │      └─example
 │  │          └─yangbibackend
 │  │              ├─common
 │  │              │  ├─constant
 │  │              │  ├─enumeration
 │  │              │  ├─exception
 │  │              │  ├─result
 │  │              │  └─utils
 │  │              ├─config
 │  │              ├─controller
 │  │              ├─hander
 │  │              ├─interceptor
 │  │              ├─manager
 │  │              ├─mapper
 │  │              ├─mq
 │  │              ├─pojo
 │  │              │  ├─DTO
 │  │              │  │  ├─chart
 │  │              │  │  ├─common
 │  │              │  │  └─user
 │  │              │  ├─entity
 │  │              │  └─VO
 │  │              │      ├─chart
 │  │              │      └─user
 │  │              ├─service
 │  │              │  └─impl
 │  │              └─webSocket
 │  └─resources
 │      └─mapper
 └─test
     └─java
         └─com
             └─example
                 └─yangbibackend
                     ├─manager
                     ├─mapper
                     ├─mq
                     └─service
                         └─impl
```