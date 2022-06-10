---
title: 在Docker上部署Hexo
categories: 代码笔记
tags: [Hexo]
date: 2020-12-07 18:04:00
url: deploy-hexo-on-docker
---
遵循小题大做原则，除了将 Hexo 属于在 Docker 中，还将实现，Hexo 托管于代码管理平台，当我们使用 git 发起 push 操作之后，容器可自动部署生成新的 Hexo 博客页面。此番折腾下来，将涉及到 Docker、Nginx、Hexo、pm2、Linux脚本编写等方面知识。

<!--More-->

先将域名解析指向 VPS，然后在进行下面的操作

## 安装Docker

> Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的镜像中，然后发布到任何流行的 Linux 或 Windows 机器上，也可以实现虚拟化。容器是完全使用沙箱机制，相互之间不会有任何接口。

以系统为 **CentOS Linux release 8.2.2004 (Core)**  的VPS为例，安装 Docker

**官网教程：**[https://docs.docker.com/engine/install/](https://docs.docker.com/engine/install/)

### 卸载旧版本

不管有没有，先运行一遍就完事了

```bash
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

### 安装新版本

接着先安装 yum-utils 软件包，之后设置稳定版仓库

```bash
$ sudo yum install -y yum-utils

$ sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

安装 Docker

```bash
$ sudo yum install docker-ce docker-ce-cli containerd.io
```

### 测试Docker

这时 Docker 已经安装成功了，为了验证是否正确的安装了 Docker 可以通过下面命令测试一下

```bash
$ sudo docker run hello-world
```

如果可以输出参考信息，不报错的话，就表示安装成功了

### Docker 信息

```bash
$ sudo docker version
```

```bash
Client: Docker Engine - Community
 Version:           19.03.14
 API version:       1.40
 Go version:        go1.13.15
 Git commit:        5eb3275d40
 Built:             Tue Dec  1 19:19:47 2020
 OS/Arch:           linux/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          19.03.14
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.13.15
  Git commit:       5eb3275d40
  Built:            Tue Dec  1 19:18:24 2020
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.3.9
  GitCommit:        ea765aba0d05254012b0b9e595e995c09186427f
 runc:
  Version:          1.0.0-rc10
  GitCommit:        dc9208a3303feef5b3839f4323d9beb36df0a9dd
 docker-init:
  Version:          0.18.0
  GitCommit:        fec3683
```

## 安装Nginx

> Nginx 是一款轻量级的 Web 服务器/反向代理服务器及电子邮件（IMAP/POP3）代理服务器，在BSD-like 协议下发行。其特点是占有内存少，并发能力强，事实上 Nginx  的并发能力在同类型的网页服务器中表现较好。

Nginx 在这里主要作用就反向代理，已经Https证书配置

**Docker_Hub：**[https://hub.docker.com/_/nginx](https://hub.docker.com/_/nginx)

Docker Hub 中包含了 Nginx 镜像 ，直接拉取下来，安装即可。但由于需要更改 Nginx 的配置文件，所以还需要创建一个容器里的 nginx.conf 和 VPS 的 nginx.conf 文件映射，如果要使用 Https 协议，也最好简历一个映射文件夹，方便管理证书

### 文件夹映射

在 /home 目录创建文件

```bash
$ mkdir /home/nginx /home/nginx/config /home/nginx/logs/ /home/nginx/ssl
```

### 安装Nginx

```bash
$ docker run  -p 443:443 -p 80:80 --name nginx -v /home/nginx/config/nginx.conf:/etc/nginx/nginx.conf -v /home/nginx/logs:/var/log/nginx -v /home/nginx/ssl:/etc/nginx/ssl -v /home/nginx/dhparam.pem:/etc/nginx/dhparam.pem -d nginx
```

## 安装NODE

接下来，这个是重点了，这里将实现自动部署构建，之所以使用 node ，而不是直接使用 Hexo 镜像，是因为需要通过 npm 安装 pm2，管理hexo，已经自动化部署脚本，所以直接使用 node 镜像了

同样，我们需要有一个映射目录，这样方便管理

```bash
$ mkdir /home/blog
```

###  安装容器

6666 端口，用来监听 WebHook，4000端口，用来 Hexo 运行，并给此容器起名为 blog

```bash
$ docker run -itd --name blog -p 4000:4000 -p6666:6666 -v /home/blog:/etc/blog node
```

### 安装 hexo 和 pm2

首先进入运行的容器：

```bash
$ docerk exec -it blog /bin/bash
```

然后直接采用全局安装的方式，安装 hexo 和 pm2

```bash
$ npm install -g hexo-cli pm2
```

### Gitee 准备

我建议，新建一个私人仓库，用来存放 Hexo，我使用的是 Gitee 平台，创建过程就省略了

接着在 blog 容器里，运行下面命令，并敲三次回车，生成 SSH 公钥

> xxxxx@xxxxx.com 改为你自己的邮箱

```bash
$ ssh-keygen -t rsa -C "xxxxx@xxxxx.com"  
```

查看公钥

```bash
$ cat ~/.ssh/id_rsa.pub
```

将生成的公钥复制粘贴到 Gitee 平台的个人账户里

打开这个链接 [https://gitee.com/profile/sshkeys](https://gitee.com/profile/sshkeys) 将公钥复制进去，保存即可

### WebHook 

URL地址，自己填一个地址，然后密码也自己输入一个，最后点击添加

![WebHook ](/images/hexo/13/20201207175839.png)

再次进入容器，将项目 [GiteeWebHook](https://gitee.com/geshuyong/GiteeWebHook) 复制到 blog 容器里，并安装服务

```bash
$ cd /etc/blog
$ git clone https://gitee.com/geshuyong/GiteeWebHook.git
$ cd GiteeWebHook
$ npm install
```

修改  [GiteeWebHook](https://gitee.com/geshuyong/GiteeWebHook) 项目配置文件

这里因为容器里没有装 vi 或 vim，可以装一个，也可以新建一个远程终端，在 VPS 里操作，我就新建一个终端，在 VPS 里操作了

> 以下命令在 VPS 中操作

```bash
$ cd /home/blog/GiteeWebHook
$ vim config.js
```
改为下面配置并保存


```json
module.exports = {
	os: "linux",					//系统类型：windows\linux
	path: "/auto_build",			//WebHook POST路径
	secret: "123456",				//请求密码
	port: 6666						//WEB Hook服务端口号
};
```

修改自动部署脚本

```bash
$ cd /home/blog/GiteeWebHook/cmd
$ vim WEB-Server.sh
```

改为下面配置并保存

```bash
#! /bin/bash

SITE_PATH='/etc/blog/hexo'
WEB_USER='root'
WEB_USERGROUP='root'

echo "开始部署"
echo "停止hexo"
pm2 stop blog
pm2 delete blog
echo "拉取最新代码"
cd /etc/blog/hexo
echo "当前工作目录"
pwd
git reset --hard
git clean -df
git pull origin master
echo "构建项目"
npm install
hexo g
echo "运行hexo"
pm2 start --name blog hexo -- s
echo "部署结束"
```

修改 index.js

```bash
$ vim /home/blog/GiteeWebHook/index.js
```

找到里面的 handler.on 方法改为下面代码

```javas
handler.on('Push Hook', function (event) {
	logger.info('Received a push event for %s to %s',
		event.payload.repository.name,
		event.payload.ref
	);
			logger.info('执行脚本')
			linux_run_cmd('sh', ['./cmd/WEB-Server.sh']);//需要执行的脚本位置
});
```

到此 WebHook 的准备工作完成了

## 安装Hexo博客

电脑需安装 Node.js，安装完成之后，打开CMD进入到D盘，运行下面命令

```bash
$ npm install hexo-cli -g
$ hexo init blog
```

进入到blog文件夹，删除里面的.git文件夹，这样才能关联上面我们的私人的仓库

```bash
$ git init
$ git add .
$ git commit -m "first commit"
$ git remote add origin 仓库地址
$ git push origin master
```

## PM2

> PM2是具有内置负载平衡器的Node.js应用程序的生产过程管理器。它使您可以使应用程序永远保持活动状态，无需停机即可重新加载它们，并简化常见的系统管理任务

我们使用 PM2 来管理自动化部署脚本，以及监听Gitee的发送过来的Push 通知

### 启动监听脚本

以下操作需要进入到 blog 容器里，

```bash
$ docker exec -it blog /binb/bash
```

进入之后，启动监听脚本

```bash
$ cd /etc/blog/GiteeWebHook
$ pm2 start index.js
```

### 克隆Gitee仓库

> 这步很重要，不能跳过

克隆前面的私人仓库

```bash
$ mkdir /etc/blog/hexo
$ cd /etc/blog/hexo
$ git clone 仓库地址
```

到这里PM2的步骤完成了，在脚本里会自动创建 hexo 运行服务，不用手动启动

## 修改反向代理

此步骤在 VPS 中操作

```bash
$ cd /home/nginx/config
$ vim nginx.conf
```

需要注意的是，在反向代理的配置 proxy_pass ，可以通过下面命令，查看Docker 容器的IP地址，将这个地址，替换 proxy_pass 后面的地址

```bash
$ docker inspect blog
```

![容器地址](/images/hexo/13/20201207183633.png)



nginx.conf的配置，需要在 vps 的 /home/nginx/ssl 目录存放 SSL 证书要然会报错，下面是我的 Nginx 配置

```bash
user www-data;
pid /run/nginx.pid;
worker_processes auto;
worker_rlimit_nofile 65535;

events {
  multi_accept on;
  worker_connections 65535;
}

http {
  charset utf-8;
  sendfile on;
  tcp_nopush on;
  tcp_nodelay on;
  server_tokens off;
  log_not_found off;
  types_hash_max_size 2048;
  client_max_body_size 16M;

  # MIME
  include mime.types;
  default_type application/octet-stream;

  # Logging
  access_log /var/log/nginx/jenkins.liaocp.cn_access.log;
  error_log /var/log/nginx/jenkins.liaocp.cn_error.log warn;

  # SSL
  ssl_session_timeout 1d;
  ssl_session_cache shared:SSL:10m;
  ssl_session_tickets off;

  # Diffie-Hellman parameter for DHE ciphersuites
  ssl_dhparam /etc/nginx/dhparam.pem;

  # Mozilla Intermediate configuration
  ssl_protocols TLSv1.2 TLSv1.3;
  ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;

  # OCSP Stapling
  ssl_stapling on;
  ssl_stapling_verify on;
  resolver 1.1.1.1 1.0.0.1 8.8.8.8 8.8.4.4 208.67.222.222 208.67.220.220 valid=60s;
  resolver_timeout 2s;

  # Load configs
  include              /etc/nginx/conf.d/*.conf;
  server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name jenkins.liaocp.cn;

    # SSL
    ssl_certificate /etc/nginx/ssl/jenkins.liaocp.cn.pem;
    ssl_certificate_key /etc/nginx/ssl/jenkins.liaocp.cn.key;

    # security
    # security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header Referrer-Policy "no-referrer-when-downgrade" always;
    add_header Content-Security-Policy "default-src 'self' http: https: data: blob: 'unsafe-inline'" always;
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;

    # . files
    location ~ /\.(?!well-known) {
      deny all;
    }

    # reverse proxy
    location / {
      proxy_pass http://172.17.0.2:4000;
      proxy_http_version 1.1;
      proxy_cache_bypass $http_upgrade;

      # Proxy headers
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "upgrade";
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
      proxy_set_header X-Forwarded-Host $host;
      proxy_set_header X-Forwarded-Port $server_port;

      # Proxy timeouts
      proxy_connect_timeout 60s;
      proxy_send_timeout 60s;
      proxy_read_timeout 60s;
    }

    # additional config
    # favicon.ico
    location = /favicon.ico {
      log_not_found off;
      access_log off;
    }

    # robots.txt
    location = /robots.txt {
      log_not_found off;
      access_log off;
    }

    # gzip
    gzip on;
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6;
    gzip_types text/plain text/css text/xml application/json application/javascript application/rss+xml application/atom+xml image/svg+xml;
  }

  # subdomains redirect
  server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name *.jenkins.liaocp.cn;

    # SSL
    ssl_certificate /etc/nginx/ssl/jenkins.liaocp.cn.pem;
    ssl_certificate_key /etc/nginx/ssl/jenkins.liaocp.cn.key;
    return 301 https://jenkins.liaocp.cn$request_uri;
  }

  # HTTP redirect
  server {
    listen 80;
    listen [::]:80;
    server_name .jenkins.liaocp.cn;
    return 301 https://jenkins.liaocp.cn$request_uri;
  }

  server {
    listen 80;
    listen [::]:80;
    server_name jenkins.liaocp.cn;
    return 301 https://jenkins.liaocp.cn$request_uri;
  }
  
  server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name www.liaocp.cn;

    # SSL
    ssl_certificate /etc/nginx/ssl/www.liaocp.cn.pem;
    ssl_certificate_key /etc/nginx/ssl/www.liaocp.cn.key;

    # security
    # security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header Referrer-Policy "no-referrer-when-downgrade" always;
    add_header Content-Security-Policy "default-src 'self' http: https: data: blob: 'unsafe-inline'" always;
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;

    # . files
    location ~ /\.(?!well-known) {
      deny all;
    }

    # reverse proxy
    location / {
      proxy_pass http://172.17.0.2:4000;
      proxy_http_version 1.1;
      proxy_cache_bypass $http_upgrade;

      # Proxy headers
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "upgrade";
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
      proxy_set_header X-Forwarded-Host $host;
      proxy_set_header X-Forwarded-Port $server_port;

      # Proxy timeouts
      proxy_connect_timeout 60s;
      proxy_send_timeout 60s;
      proxy_read_timeout 60s;
    }
	
	# reverse proxy
    location /auto_build {
      proxy_pass http://172.17.0.2:6666;
      proxy_http_version 1.1;
      proxy_cache_bypass $http_upgrade;

      # Proxy headers
      proxy_set_header Upgrade $http_upgrade;
      proxy_set_header Connection "upgrade";
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
      proxy_set_header X-Forwarded-Host $host;
      proxy_set_header X-Forwarded-Port $server_port;

      # Proxy timeouts
      proxy_connect_timeout 60s;
      proxy_send_timeout 60s;
      proxy_read_timeout 60s;
    }

    # additional config
    # favicon.ico
    location = /favicon.ico {
      log_not_found off;
      access_log off;
    }

    # robots.txt
    location = /robots.txt {
      log_not_found off;
      access_log off;
    }

    # gzip
    gzip on;
    gzip_vary on;
    gzip_proxied any;
    gzip_comp_level 6;
    gzip_types text/plain text/css text/xml application/json application/javascript application/rss+xml application/atom+xml image/svg+xml;
  }

  # subdomains redirect
  server {
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name *.www.liaocp.cn;

    # SSL
    ssl_certificate /etc/nginx/ssl/www.liaocp.cn.pem;
    ssl_certificate_key /etc/nginx/ssl/www.liaocp.cn.key;
    return 301 https://www.liaocp.cn$request_uri;
  }

  # HTTP redirect
  server {
    listen 80;
    listen [::]:80;
    server_name .www.liaocp.cn;
    return 301 https://www.liaocp.cn$request_uri;
  }

  server {
    listen 80;
    listen [::]:80;
    server_name www.liaocp.cn;
    return 301 https://www.liaocp.cn$request_uri;
  }
  
}
```

## 最后

重启下 Nginx 容器，就可以了，当给私人仓库 push 操作时候，VPS 就会自动执行部署脚本，完成 Heox 页面生成并启动

```bash
$ docker restart nginx
```

