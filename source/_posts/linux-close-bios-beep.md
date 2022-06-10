---
title: 关闭Linux删除键发出的蜂鸣声(嘟～)
categories: 系统折腾
tags: [Deepin]
date: 2021-03-07 16:58:51
url: linux-close-bios-beep
---

该响铃是因为红帽企业Linux默认安装并加载了 **pcspkr** 内核模块导致，所以这个问题根源并不是声卡发出的，而是主板的蜂鸣器。

<!--more-->

## 我的系统环境

```shell
Linux version 5.4.70-amd64-desktop (deepin@deepin-PC) (gcc version 8.3.0 (Uos 8.3.0.3-3+rebuild)) #1 SMP Wed Oct 14 15:24:23 CST 2020
```

## 解决办法

解决办法有两种，第一种亲测有效，第二种没试过

### 当前用户永久生效

如果用的是bash作shell，在当前用户目录 **~/.bashrc** 的最后添加

```shell
xset -b
```

之后运行下面命令，即可生效

```shell
source .bashrc
```



### 所有用户永久生效

```shell
echo "rmmod pcspkr" >>　/etc/rc.d/rc.local
```

这条命令的意思是在 **/etc/rc.d/rc.local** 脚本中追加一，内容为：**rmmod pcspkr**。这样做的目的是让系统每次在开机的时候删除 **pcspkr** 内核模块，而 **rc.local** 一般不管你在 **3** 的启动模式还是 **5** 的启动模式下，这个服务脚本都会执行，把这个 **pcspkr** 内核模块删除了，那不管是在什么模式，以什么用户登录，自然就无法加载，也就不会再发出那烦人的嘟嘟嘟的报警声了。