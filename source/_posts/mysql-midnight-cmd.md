---
title: 禁止MySql半夜十二点的定时任务
url: mysql-midnight-cmd
date: 2022-02-27 00:04:57
tags: [Mysql]
categories: 数据库
photos: [/thumbnail/mysql.webp]
---

MySql数据在半夜十二点，总会自动弹出一个CMD。可以在Windows系统定时任务中关闭，也可以在Mysql中关闭。

<!--more-->

![3](3.jpg)

## Windows 关闭（推荐）
 开始菜单搜索`任务计划程序`，找到MySql里面的那个任务，右键状态改为禁用。

 ![任务计划程序](2.jpg)

 ## Mysql 设置

 开始菜单搜索`Mysql Install`，点击右上角的扳手，弹出窗口中，取消勾选，最后点击确定。

 ![Mysql install](1.jpg)