---
title: Linux 创建 Jetbrains 程序图标
categories: 系统折腾
tags: [Ubuntu]
date: 2021-03-15 22:56:38
url: linux-create-jetbrains-desktop-entry
---

也不知道从哪个版本开始，Jetbrains 系列软件，第一次运行时候，不会有引导程序，就不会给自动创建快捷方式。目前我的版本是 2020.3.2 ，正好记录下在 Linux 环境创建程序图标。

<!--more-->

## 位置

应用程序图标配置，通常位放于三个位置，每个位置表示含义不一样，我一般习惯放第一个位置

* **系统程序，所有用户显示：/usr/share/applications**
* **系统程序，所有用户显示：/usr/local/share/applications**
* **当前用户显示，其他用户不显示：~/.local/share/applications**

## Desktop Entry

在对应目录创建 **.desktop** 后缀结尾的文件即可，其中 **Type** 和 **Name** 是必须

**.desktop** 文件规范：[https://specifications.freedesktop.org/desktop-entry-spec/desktop-entry-spec-latest.html#recognized-keys](https://specifications.freedesktop.org/desktop-entry-spec/desktop-entry-spec-latest.html#recognized-keys)

Categories 规范：[https://specifications.freedesktop.org/menu-spec/latest/apa.html](https://specifications.freedesktop.org/menu-spec/latest/apa.html)

## IntelliJ Idea

>  Exec 为执行脚本位置；Icon 为程序图标位置

```shell
sudo gedit /usr/share/applications/IntelliJIdea.desktop
```

```shell
[Desktop Entry]
Exec=/home/liaocp/idea-IU/bin/idea.sh
GenericName=IntelliJ Idea
GenericName[zh_CN]=IntelliJ Idea
Name=IntelliJ Idea
Name[zh_CN]=IntelliJ Idea
StartupNotify=false
Terminal=false
Type=Application
Categories=Development;
Icon=/home/liaocp/idea-IU/bin/idea.svg
```

保存文件。

## WebStorm

> Exec 为执行脚本位置；Icon 为程序图标位置

```shell
sudo gedit /usr/share/applications/WebStorm.desktop
```

```shell
[Desktop Entry]
Exec=/home/liaocp/WebStorm/bin/webstorm.sh
GenericName=WebStorm
GenericName[zh_CN]=WebStorm
Name=IntelliJ Idea
Name[zh_CN]=WebStorm
StartupNotify=false
Terminal=false
Type=Application
Categories=Development;
Icon=/home/liaocp/WebStorm/bin/webstorm.svg
```

保存文件。
