---
title: 升级NexT主题注意事项
categories: 代码笔记
tags: [Hexo]
date: 2021-04-07 23:33:16
url: update-hexo-theme-next
---

对博客使用的NexT主题升级到了目前最新版8.3.0，记录一下升级需要注意的地方，下次升级就不要继续踩坑了。

<!--more-->

## 静态资源

在主题的 `/themes/next/source` 目录里面存放了一些静态的资源，`images` 存放了一下与网站相关的图片，例如 logo、favicon、头像等之类的图标，如果有更改过，需要先备份出来，之后覆盖回去。

## 配置文件

配置文件的升级，一般来说都会继承前面版本的配置，但也有例外，所以需要关注下 [https://github.com/next-theme/hexo-theme-next/issues](https://github.com/next-theme/hexo-theme-next/issues) 发布的更新说明。

## 升级记录

这次升级8.3.0中，移除了配置文件中`Valine`评论系统，以及`pace`加载动画的支持。

* 仍需使用 `Valine`，需安装插件使用[`hexo-next-valine`](https://github.com/next-theme/hexo-next-valine)，主题配置文件可沿旧版本的关于 `Valine` 的配置

* `pace` 改为 `nprogress `