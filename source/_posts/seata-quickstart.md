---
title: seata 快速入门
date: 2022-04-12 09:52:20
tags: [微服务]
categories: [代码笔记]
url: seata-quickstart
photos: [/thumbnail/seata.webp]
---



Seata 是一款开源的分布式事务解决方案，致力于提供高性能和简单易用的分布式事务服务。Seata 将为用户提供了 AT、TCC、SAGA 和 XA 事务模式，为用户打造一站式的分布式解决方案。

<!--more-->



## 分布式事务处理过程



1个ID+3个组件



### transaction ID -- XID



全局唯一的事务ID



### 3个组件

* TC (Transaction Coordinator) - 事务协调者：维护全局和分支事务的状态，驱动全局事务提交或回滚。
* TM (Transaction Manager) - 事务管理器：定义全局事务的范围：开始全局事务、提交或回滚全局事务。
* RM (Resource Manager) - 资源管理器：管理分支事务处理的资源，与TC交谈以注册分支事务和报告分支事务的状态，并驱动分支事务提交或回滚。



### 处理过程

1. TM 向 TC 申请开启一个全局事务，全局事务创建成功并生成一个全局唯一的 XID ；
2. XID 在微服务调用链路的上下文中传播；
3. RM 向 TC 注册分支事务，将其纳入 XID 对应全局事务的管辖；
4. TM 向 TC 发起针对 XID 的全局提交或回滚决议；
5. TC 调度 XID 下管辖的全部分支事务完成提交或回滚。
![Seate 组件处理过程](./Seate组件处理过程.jpg)



## 下载中心

[https://seata.io/zh-cn/blog/download.html](https://seata.io/zh-cn/blog/download.html)



## 框架用法

将 `@GlobalTransactional` 注解，加入业务方法上即可。



## 安装



### 修改配置



修改为数据库模式，来存储事务日志。

1. 找到 `conf/file.conf` 文件，将 `store.mode` 改为 `db` ；
2. 找到 `db {...}` 标签，更改为对应的数据库链接信息；
3. [下载脚本](https://github.com/seata/seata/tree/develop/script/server/db)，找到对应数据库建表脚本，并执行。



修改 nacos 为注册中心。

1. 找到 `conf/registry.conf` ，将 `registry.type` 改为 `nacos` ；
2.  找到 `nacos {...}` 标签，更改为对应的 Nacos 信息。



### 运行



由于我们修改注册中心为 nacos ，需先启动 nacos。



然后找到 `bin` 目录，选择合适的脚本运行服务。
