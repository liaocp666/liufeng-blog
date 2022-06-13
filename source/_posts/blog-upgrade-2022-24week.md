---
title: 博客升级记录 - 2022年24周
date: 2022-06-12 00:10:43
tags: [博客, 分享, 我]
categories: 系统折腾
url: blog-upgrade-2022-24week
keywords: [博客, 分享, 我, 博客升级]
description: 2022年24周对博客进行了一些升级，这次升级主要针对Hexo，博客功能、界面，博客托管平台，以及博客自动化构建生成。
photos: [/thumbnail/upgrade.webp]
---

2022年24周对博客进行了一些升级，这次升级主要针对Hexo，博客功能、界面，博客托管平台，以及博客自动化构建生成。

<!--more-->

## Hexo

* Hexo 版本升级至 6.2.0

## 博客功能

* 增加SEO优化，页面meta标签加入keywords、description
* 增加[RSS订阅](https://www.kokoo.top/atom.xml)、网站地图[sitemap.xml](https://www.kokoo.top/sitemap.xml)
* 增加utterances评论功能

## 博客界面

* 弱化阴影，更改网站底色
* 标题、内容等文字信息左对齐
* 缩略图改为左右布局

## 托管平台

* 切换 [Github](https://github.com/liaocp666/blog) 托管

## 自动构建

* 使用 [java-webhook](https://github.com/liaocp666/java-webhook) 项目，监听 webhook，实现自动构建博客
