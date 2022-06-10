---
title: CSS 网页字体最佳实践
date: 2022-06-07 06:34:39
tags: [前端, CSS]
categories: [代码笔记]
url: css-font-optimization
keywords: [前端, CSS, 网页字体]
description: 一般在网页的字体设置中，可以将字体分类三类：系统字体、兜底字体、Emoji字体。为了满足不同平台，以及Emoji更好的显示效果（不会被前面的系统字体或兜底字体覆盖），建议使用下面代码配置网页字体。
photos: [/images/20220607083627.png]
---

一般在网页的字体设置中，可以将字体分类三类：

1. 系统字体：使用系统自带的字体
2. 兜底字体：当系统字体无法正常使用，而兜底的字体
3. Emoji 字体：显示网页中的表情字体

为了满足不同平台，以及Emoji更好的显示效果（不会被前面的系统字体或兜底字体覆盖），建议使用下面代码配置网页字体。

## 最佳实践

```css
@font-face {
  font-family: Emoji;
  src: local("Apple Color Emojiji"), local("Segoe UI Emoji"), local("Segoe UI Symbol"), local("Noto Color Emoji");
  unicode-range: U+1F000-1F644, U+203C-3299;
}
body {
  font-family: system-ui, —apple-system, Segoe UI, Rototo, Emoji, Helvetica, Arial, sans-serif;
}
```

衬线字体

```css
.font-serif {
  font-family: Georgia, Cambria, "Times New Roman", Times, serif;
}
```

等宽字体

```css
.font-mono {
  font-family: Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace;
}
```

## 解释说明

system-ui 是 [CSS Fonts Module Level 4 -- Generic font families](https://www.w3.org/TR/css-fonts-4/#generic-font-families) 新增的关键字，兼容不同平台版本浏览器。
![system-ui](/images/20220607083627.png)

考虑到不同平台及向后兼容，在 macOS 和 iOS 上，我们需要使用 `-apple-system`。

<iframe src="//player.bilibili.com/player.html?aid=854182702&bvid=BV1b54y1Z7pu&cid=721855706&page=1" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true"> </iframe>