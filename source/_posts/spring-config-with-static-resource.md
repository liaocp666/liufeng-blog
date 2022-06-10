---
title: Spring 静态资源访问配置
url: spring-config-with-static-resource
date: 2022-06-02 15:45:40
tags: [Sping, SpringBoot]
categories: [代码笔记]
description: Spring Boot 默认情况下提供了来自 classpath 路径下/static、/public、/resources 和 /META-INF/resources_ 目录访问。例如将一个 about.html 页面放在 static 目录下面，在浏览器则可以通过 http://server:port/about.html 路径来访问到 about.html 这个静态资源。
photos: [/thumbnail/spring-framework.webp]
---

Spring Boot 默认情况下提供了来自 classpath 路径下 /static、/public、/resources 和 /META-INF/resources_ 目录访问。

例如将一个 about.html 页面放在 static 目录下面，在浏览器则可以通过 http://server:port/about.html 路径来访问到 about.html 这个静态资源。

## 配置访问路径

默认情况下，Spring Boot 访问静态资源都是通过根路径来访问的：/** ，当我们想要将 /** 更改为 /blog 时，可以通过修改配置文件来实现：

```properties
spring.mvc.static-path-pattern=/blog/**
```

## 配置访问目录

与上面的配置相似，我们也可以通过额外的配置，来实现访问其它的静态资源目录。不过需要注意的是：

当要添加的目录在 classpath 路径下，需要以 classpath: 开头，多个使用英文逗号分隔。

```properties
spring.web.resources.static-locations=classpath:/files/
```

当要添加的目录不在 classpath 路径下时，需要以 file: 开头，file: 与 http: 表达的意思类似，http: 可以理解为从网络上加载资源，file: 可以理解为从本地磁盘加载资源。多个使用英文逗号分隔。

> file: 后面两个斜杠可加可不加，建议加上。因为就像使用 http 协议访问网络资源，通常是以 http:// 开头。
>
> [The ResourceLoader](https://docs.spring.io/spring-framework/docs/5.2.4.RELEASE/spring-framework-reference/core.html#resources-resourceloader)


```properties
spring.web.resources.static-locations=file:///opt/files
```

## 配置类

Spring3.1 后支持配置类的方式来配置项目，推荐使用此方式进行配置。

创建 MvcConfig 类，实现 WebMvcConfigurer 接口，并添加注解 @Configuration。

```java
@Configuration 
public class MvcConfig implements WebMvcConfigurer {
	@Override
	public void addResourceHandlers(ResourceHandlerRegistry registry) {
	 registry
		 // 配置访问路径
		 .addResourceHandler("/files/**")
		 // 配置访问目录，一个访问路径可配置多个访问目录
		 .addResourceLocations("file:/opt/files/");
	}
}
```
