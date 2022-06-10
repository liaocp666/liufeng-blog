---
title: Spring Gateway 内置断言笔记
url: Spring-Gateway-Route-Predicate-Factories
date: 2022-02-28 00:01:56
tags: [微服务, SpringCloud]
categories: [代码笔记]
photos: [/thumbnail/spring-cloud-gateway.webp]
---
以3.1.1为例，Spring网关内置了12种，用来匹配HTTP不同属性的断言工厂，可以直接使用配置的形式来使用，并且可以多个联合使用。

<!--more-->

<a name="FPXpy"></a>
## 配置文件
```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: after_route
        uri: https://example.org
        predicates:
        - After=2017-01-20T17:42:47.789-07:00[America/Denver]
```
<a name="EGnmM"></a>
## 配置类
```java
@Configuration
public class GatewayConfig {
    @Bean
    public RouteLocator routes(RouteLocatorBuilder builder) {
        return builder.routes()
                .route("news2", r -> r.path("/guoji").uri("http://news.baidu.com"))
                .build();
    }
}
```
<a name="GmtAG"></a>
## 内置断言
<a name="UkZRk"></a>
### After Route Predicate Factory
匹配在指定日期时间之后发起的请求，参数是 [[ZoneDateTime](https://www.liaoxuefeng.com/wiki/1252599548343744/1303904694304801)] 对象
```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: after_route
        uri: https://example.org
        predicates:
        - After=2017-01-20T17:42:47.789-07:00[America/Denver]
```
<a name="fxyOd"></a>
### Before Route Predicate Factory
匹配在指定日期之前发起的请求，参数是 [[ZoneDateTime](https://www.liaoxuefeng.com/wiki/1252599548343744/1303904694304801)] 对象
```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: before_route
        uri: https://example.org
        predicates:
        - Before=2017-01-20T17:42:47.789-07:00[America/Denver]
```
<a name="IOEUM"></a>
### Between Route Predicate Factory
匹配在指定日期中间发起的请求，参数是两个 [[ZoneDateTime](https://www.liaoxuefeng.com/wiki/1252599548343744/1303904694304801)] 对象
```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: between_route
        uri: https://example.org
        predicates:
        - Between=2017-01-20T17:42:47.789-07:00[America/Denver], 2017-01-21T17:42:47.789-07:00[America/Denver]
```
<a name="uC8Iu"></a>
### Cookie Route Predicate Factory
匹配带有指定 Cookie 数据的请求，有两个参数，第一个是 Cookie 名称，后面是一个正则表达式，用来匹配  Cookie 的内容
```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: cookie_route
        uri: https://example.org
        predicates:
        - Cookie=chocolate, ch.p
```
<a name="rfcRg"></a>
### Header Route Predicate Factory
匹配带有指定 Header 的请求，有两个参数，第一个是 Header 名称，后面是一个正则表达式，用来匹配 Header 的内容
```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: header_route
        uri: https://example.org
        predicates:
        - Header=X-Request-Id, \d+
```
<a name="cxw3z"></a>
### Host Route Predicate Factory
匹配请求中 Host 数据，采用的是 [Ant-Style](https://blog.csdn.net/my__Sun_/article/details/76444435) 方式匹配，多个用英文逗号分隔
```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: host_route
        uri: https://example.org
        predicates:
        - Host=**.somehost.org,**.anotherhost.org
```
<a name="Hc6su"></a>
### Method Route Predicate Factory
匹配指定方式的请求，参数是 [HTTP Methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)
```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: method_route
        uri: https://example.org
        predicates:
        - Method=GET,POST
```
<a name="JK2K3"></a>
### Path Route Predicate Factory
匹配指定的路径请求，这也是最常用的一个断言。有两个参数：`路径表达式(Spring PathMatcher patterns)`集合和一个名为`matchTrailingSlash(默认为true)`的参数。
```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: path_route
        uri: https://example.org
        predicates:
        - Path=/red/{segment},/blue/{segment}
```
如果请求路径是，则此路由匹配，例如：`/red/1`or `/red/1/`或`/red/blue`or `/blue/green`。<br />如果`matchTrailingSlash`设置为`false`，则请求路径`/red/1/`将不匹配。
<a name="fNNdu"></a>
### Query Route Predicate Factory
匹配带有指定参数的请求，有两个参数，第一个是参数名，第二个是正则参数值(可选)<br />第一种情况：带`green`参数的请求，例如：http://localhost/color?green=1
```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: query_route
        uri: https://example.org
        predicates:
        - Query=green
```
第二种情况：带有`red`参数名和`gree.`正则的请求，例如：http://localhost/color?red=gree
```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: query_route
        uri: https://example.org
        predicates:
        - Query=red, gree.
```
<a name="Fqx7Z"></a>
### RemoteAddr Route Predicate Factory
匹配发起请求中 RemoteAddr 部分的数据，参数为IPv4 或 IPv6 的地址字符串
```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: remoteaddr_route
        uri: https://example.org
        predicates:
        - RemoteAddr=192.168.1.1/24
```
<a name="TvqVv"></a>
### Weight Route Predicate Factory
将更多的请求，指向权重更高的路由。有两个参数，第一个为组名，第二个为该组的权重
```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: weight_high
        uri: https://weighthigh.org
        predicates:
        - Weight=group1, 8
      - id: weight_low
        uri: https://weightlow.org
        predicates:
        - Weight=group1, 2
```
此配置会将80%的请求，转发至 `https://weighthigh.org` ；将20%请求，转发至 `https://weightlow.org`
<a name="ky4Ol"></a>
### XForwarded Remote Addr Route Predicate Factory
匹配请求中 X-Forwarded-For 的数据，参数为 IPv4 或 IPv6 的单个字符串或集合。
```yaml
spring:
  cloud:
    gateway:
      routes:
      - id: xforwarded_remoteaddr_route
        uri: https://example.org
        predicates:
        - XForwardedRemoteAddr=192.168.1.1/24
```
