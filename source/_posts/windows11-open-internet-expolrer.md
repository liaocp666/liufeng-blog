---
title: Windows11使用IE的三个方法
url: windows11-open-internet-expolrer
date: 2022-02-17 02:15:52
tags: [Windows11]
categories: [系统折腾]
photos: [/thumbnail/windows11.webp]
---

经过26年的漫长岁月，IE浏览器终于在Windows系统中慢慢消失。

<!--more-->

由于一些历史原因，国内的一些官方网站，还是需要使用IE浏览器来访问。但是在最新的Windows11系统中，IE浏览器已经不见了，不过微软也并没有彻底封死IE，仍然可以通过一些途径，来使用Trident MSHTML 引擎访问。

建议直接使用方法二更快捷。

## 方法一：开启IE模式

 Internet Explorer 模式是Edge浏览器里提供的功能，也是正规能使用到IE模式来访问的方法。

1. 开启Edge，访问网址：**edge://settings/defaultBrowser**

2. "让 Internet Explorer 在 Microsoft Edge 中打开网站" 栏目设置为 **从不**

3. "允许在 Internet Explorer 模式下重新加载网站" 栏目设置为 **允许**

4. "Internet Explorer 模式页面" 栏目添加需要使用IE模式访问的网址

5. 重启 Edge

按上面步骤设置之后，浏览特定的网页就会使用IE模式来访问。

![开启IE模式](1.jpg)

### 更改IE版本

默认使用的IE11版本来访问特定的网址，但如果需要更改IE版本，就需要特殊手段进行更改。

1. 打开需要使用IE访问的网址

2. 打开资源管理器，在地址栏输入"**%systemroot%\system32\f12\IEChooser.exe**" 回车

3. 在开发人员工具中，选择要调试的目标后，右上角的IE11就可以更改版本了，有5~11这些版本可以更改。

![更改IE版本](2.png)

## 方法二：开启IE浏览器

如果上面的方法感觉太繁琐，建议使用下面方法，直接在Windows11系统里，就可以打开IE浏览器了。不过需要Windows11专业版及以上版本才有效。

> 注意：此方法仍然需要参考上面方法一的设置步骤的第2点："让 Internet Explorer 在 Microsoft Edge 中打开网站" 栏目设置为 **从不**！！！

1. 新建一个文件：IE.vbs

2. 使用记事本或其它笔记软件，编辑此文件，复制下面内容，并保存
   
   ```visual-basic
   CreateObject("InternetExplorer.Application").Visible=true
   ```

3. 双击 IE.vbs 文件，这是你就会发现IE浏览器神奇的回来啦(*^▽^*)。

![开启IE浏览器](3.jpg)

## 方法三：IE Tab

IE Tab 是Chrome浏览器的一款插件，支持Windows11系统下使用，并提供了 IE (IE7 - IE11) 的版本浏览网页。

[Chrome应用商店](https://chrome.google.com/webstore/detail/ie-tab/hehijbfgiekmjfkfjpbkbammjbdenadd) | [离线下载](https://crxdl.com/) (插件ID：hehijbfgiekmjfkfjpbkbammjbdenadd)

> 离线安装插件教程：https://crxdl.com/topic/0008-pc-chrome-crx.html

![IE Tab](4.jpg)