---
title: CentOS安装Mysql5.7
date: 2019-07-19 16:41:15
categories: 数据库
url: centos-install-mysql57
---

我看网上的其他教程都是说下载MySQL的RPM包或者DEB包，然后安装；但其实不用这样，可以使用YUM或者API命令也能安装，安装流程如下：

<!--More-->

## 安装说明

服务器是腾讯云的一个服务器，在国内，安装的系统是CentOS Linux release 7.6.1810，所以我就以RPM系为例了，相信DEB系的同学可以举一反三；要安装的MySQL版本是5.7。

## 存储库

MySQL给Linux发行版提供了一个存储库（[传送门](https://dev.mysql.com/downloads/repo/)），其中就有最新的MySQL。不过，在选择合适的存储库之前，需要选择合适的系列，也就是对应的包管理工具

![存储库](/postimg/2019/07/19/20190719165451.png)

## 开始安装

接下来就是跟着命令一条龙走了

### 1)添加存储库 

```shell
yum localinstall https://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm
```

输入”y“并按”Enter“进行确认

![添加存储库 ](/postimg/2019/07/19/20190719174421.png)

### 2)安装MySQL

```she
yum -y install mysql-community-server
```

![安装MySQL](/postimg/2019/07/19/20190719174619.png)

![安装MySQL](/postimg/2019/07/19/20190719174723.png)

### 3)启动MySQL服务

```shell
systemctl start mysqld
```

### 4)开机自启动

```shell
systemctl enable mysqld
```

### 5.1)查看是否启动

这里主要是看3306端口是否启动

```shell
netstat -plntu
```

![启动MySQL服务](/postimg/2019/07/19/20190719175224.png)

### 5.2)查看是否启动

还有一个更简单的查看方法，显示enabled则表示已经启动

```shell
systemctl is-enabled mysqld
```

![查看是否启动](/postimg/2019/07/19/20190719175407.png)

### 6)配置MySQL密码

密码在安装的时候就已经默认生成了，需要在mysqld.log日志中查看

```shell
grep 'temporary' /var/log/mysqld.log
```

![配置MySQL密码](/postimg/2019/07/19/20190719175941.png)

### 7) 登录MySQL

输入完下面命令后，回车；会要你输入密码，这个时候输入密码是不会回显的哦

```shell
mysql -u root -p
```

接着更换密码，我改成123456

```shell
ALTER USER 'root'@'localhost' IDENTIFIED BY '123456'
```

刷新系统权限

```shell
flush privileges
```

![登录MySQL](/postimg/2019/07/19/20190719180028.png)

### 8)再次登录

到这里就已经安装完成了哦

![再次登录](/postimg/2019/07/19/20190719180150.png)

