---
title: 使用 Certbot 生成 Let’s Encrypt 证书
date: 2022-06-05 16:20:21
tags: [Certbot, HTTPS]
categories: 系统折腾
url: certbot-generate-letsencrypt-certificate
keywords: [使用 Certbot 生成 Let’s Encrypt 证书, Certbot, Let’s Encrypt 证书]
description: Let’s Encrypt是一个证书颁发机构（CA）。要从这个机构获取域名证书，需要在服务器上使用支持ACME 协议的软件客户端来获取。Certbot是 Let’s Encrypt 推荐使用的软件客户端。当我们有了域名证书之后，就可以配置使用 HTTPS 来访问了。
photos: [/thumbnail/letsencrypt.webp]
---

[Let’s Encrypt](https://letsencrypt.org/) 是一个证书颁发机构（CA）。要从这个机构获取域名证书，需要在服务器上使用支持 [ACME 协议](https://tools.ietf.org/html/rfc8555) 的软件客户端来获取。

[Certbot](https://certbot.eff.org/)是 Let’s Encrypt 推荐使用的软件客户端。当我们有了域名证书之后，就可以配置使用 HTTPS 来访问了。

<!--more-->

在使用 Cerbot 之前，需要提前安装并配置好 Nginx。

## 安装snapd
执行下面命令安装snapd

```shell
sudo apt install snapd
```

## 移除已安装的 certbot
没有安装过可跳过此步骤。

如果之前有安装过 certbot，需要先手动移除一下，因为现在使用的是 snapd 方式来安装。

```shell
sudo apt-get remove certbot
```

## 安装 certbot
执行下面命令，安装 certbot

```shell
sudo snap install --classic certbot
```

## 准备 certbot 命令

```shell
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

## 运行 certbot

执行下面命令，按提示填入内容，certboot 将会自动更改 nginx 配置，并启用 https 访问。

>我在此步骤失败了，因为我的Nginx不是正常通过包管理器安装的，解决办法也很简单，参考[常见问题](#常见问题)

```shell
sudo certbot --nginx
```

如果想手动更新 nginx 配置，运行下面命令

```shell
sudo certbot certonly --nginx
```

## 自动续订

Let’s Encrypt 证书只有90天的有效期，到期后，需要再次申请。

certbot 提供自动续订功能，使用系统的 cron 或 systemd 功能，在证书到期之前，自动续订证书。

使用下面命令，可测试自动续订功能是否正常。

```shell
sudo certbot renew --dry-run
```

## 常见问题

### NoInstallationError()

出现这个问题的直接原因：certbot 在 /usr/bin 目录下找不到nginx执行文件

**解决办法**

在 /usr/bin 目录下创建nginx的软连接。我的nginx位置是 /usr/local/nginx/sbin/nginx

```shell
sudo ln -s /usr/local/nginx/sbin/nginx /usr/bin/nginx
```

使用下面命令找到 nginx 位置

```shell
sudo find / name nginx
```

如果在 /etc/nginx 目录下没有配置文件，也需要创建 nginx 的 conf 目录软链接至 /etc/nginx

```shell
sudo ln -s /usr/local/nginx/conf /etc/nginx
```