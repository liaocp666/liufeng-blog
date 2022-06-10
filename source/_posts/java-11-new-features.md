---
title: Java11新特性笔记
categories: 代码笔记
tags: [JDK]
date: 2021-03-21 12:27:54
url: java-11-new-features
---

Java11 是甲骨文于2018年9月发布，是在 8 以后发布的第一个长期支持的的版本。

<!--more-->

之前在看Spring布道师龙之春的演讲视频的时候，他在 [Spring Initiallizr](https://start.springboot.io/) 上选择Jdk版本时候，打趣的说道：

> ”这里有13、11、8，三个版本，但是选择哪个呢？“
>
> ”正确答案只有两个，13或11。“
>
> ”作为一名开发者。不会还再和孩子说，嗨，老爸我选择了jdk8……“

其实就目前国内的情况，也就一些大厂在使用11吧，中小型公司还在选择8，或者更低的版本。但长江后浪推前浪，作为一个前浪，有空也学习了11，省的被拍死在沙滩上。

## String

String作为一个常用的类，在11里面增加一些新的方法

### lines()

此方法返回 Stream<String>，作用是将一串文字，以换行符分隔并返回行流

支持的换行符有：**\n** 、**\r**、**\ r \ n** 正好对应不同操作系统里的换行符

```java
public static void main(String[] args) {
    String str = "第一行 \n第二行 \r第三行\r\n第四行\t不换行";
    str.lines().forEach(System.out::println);
}
```

**结果**

```java
第一行 
第二行 
第三行
第四行	不换行
```

### isBlank()

判断字符串是否为空白，此方法使用需要和 **isEmpty()** 方法注意，纯空格在 **isBlank()** 方法中为 **true**，在 **isEmpty()** 方否中为 **false**

```java
public static void main(String[] args) {
    String str1 = "字 符 串";
    String str2 = "";
    String str3 = " ";
    System.out.println(">>>> isBlank 方法 <<<<");
    System.out.println(str1.isBlank());
    System.out.println(str2.isBlank());
    System.out.println(str3.isBlank());
    System.out.println(">>>> isEmpty 方法 <<<<");
    System.out.println(str1.isEmpty());
    System.out.println(str2.isEmpty());
    System.out.println(str3.isEmpty());
}
```

**结果**

```java
>>>> isBlank 方法 <<<<
false
true
true
>>>> isEmpty 方法 <<<<
false
true
false
```

### strip()

去除字符串开头和结尾空格，不包括字符中间空格。

**strip()** 方法 **trim()** 作用一致，区别在于 **strip()** 方法支持 **Unicode** 字符集。

由 **strip()** 方法衍生而来，还有两个方法：

* **stripLeading()**：仅去除字符串开头空格
* **stripTrailing()**：仅去除字符串结尾空格

```java
public static void main(String[] args) {
    String str = " 人生如 逆旅，我亦 是行人。\n但愿初相遇，不负有心人。 ";
    System.out.println(">>>> strip() <<<<");
    System.out.println(str.strip());
    System.out.println(">>>> trim() <<<<");
    System.out.println(str.trim());
    System.out.println(">>>> 去除开头空格 <<<<");
    System.out.println(str.stripLeading());
    System.out.println(">>>> 去除结尾空格 <<<<");
    System.out.println(str.stripTrailing());
}
```

**结果**

```java
>>>> strip() <<<<
人生如 逆旅，我亦 是行人。
但愿初相遇，不负有心人。
>>>> trim() <<<<
人生如 逆旅，我亦 是行人。
但愿初相遇，不负有心人。
>>>> 去除开头空格 <<<<
人生如 逆旅，我亦 是行人。
但愿初相遇，不负有心人。 
>>>> 去除结尾空格 <<<<
 人生如 逆旅，我亦 是行人。
但愿初相遇，不负有心人。
```

### repeat()

将字符串重复 N 次

```java
public static void main(String[] args) {
   String str = "但愿初相遇，不负有心人。\r\n";
   System.out.println(str.repeat(3));
}
```

**结果**

```java
但愿初相遇，不负有心人。
但愿初相遇，不负有心人。
但愿初相遇，不负有心人。
```

## Files

在11中，新增了一些文件方法，可以方便的从文件中读取和写入字符串

### readString() 和 writeString()

```java
public static void main(String[] args) throws IOException {
    Path file = Files.writeString(
        Files.createTempFile("FileStudy", ".txt"), "但愿初相遇，不负有心人。");
    System.out.println(Files.readString(file));

}
```

**结果**

```java
但愿初相遇，不负有心人。
```

## Collection

新增带有 *IntFunction* 参数的方法 toArray()，作用就是将集合转为数组时候，可指定创建对应类型的数组

```java
public static void main(String[] args) {
    List<String> strList = new ArrayList<>(){{
        add("1");
        add("2");
        add("3");
    }};
    String[] strCollections = strList.toArray(String[]::new);
    System.out.println(strList);
    System.out.println(Arrays.toString(strCollections));
}
```

**结果**

```java
[1, 2, 3]
[1, 2, 3]
```

## Lambda

增加了 var 关键字修饰 Lambda 中的局部变量

```java
public static void main(String[] args) {
    List<String> strList = new ArrayList<>(){{
        add("但愿初相遇  ");
        add("   不负有心人");
    }};
    System.out.println(strList);
    String str = strList.stream()
        .map((var e) -> e.strip())
        .collect(Collectors.joining(","));
    System.out.println(str);
}
```

**结果**

```java
[但愿初相遇  ,    不负有心人]
但愿初相遇,不负有心人
```

## 编译

可直接使用 `java xxx.java` 编译运行，不用使用 `javac` 显示编译文件

![java11编译运行](/images/hexo/25/20210321140709.png)

## 其他

当然了除了上面这些在实际开发中会使用到的内容，还有其他的一些改变：https://www.baeldung.com/java-11-new-features