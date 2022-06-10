---
title: H2-Table CATALOGS not found
url: h2-table-catalogs-not-found
date: 2022-05-26 09:46:30
tags: [Spring, SpringBoot]
categories: 数据库
photos: [/thumbnail/h2-logo-2.webp]
---

在使用 IntelliJ IDEA 2021.1.3 版本，使用默认配置连接 H2 数据库的时候，出现下面错误，项目里 H2 使用的版本为 2.0.202 。

<!--more-->

>[42S02][42102] org.h2.jdbc.JdbcSQLSyntaxErrorException: Table "CATALOGS" not found; SQL statement:
>select catalog_name from information_schema.catalogs [42102-202].
>Table "CATALOGS" not found; SQL statement:
>select catalog_name from information_schema.catalogs [42102-202].
> (4 sec, 666 ms)

出现这个错误，是因为`INFORMATION_SCHEMA`有新旧两套实现。

解决办法就是在 JDBC URL 后面拼接参数：;OLD_INFORMATION_SCHEMA=TRUE

例如：jdbc:h2:file:./db/JaxBlog`;OLD_INFORMATION_SCHEMA=TRUE`
