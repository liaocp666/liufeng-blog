---
title: Java中只有严格的值传递，没有引用传递
categories: 代码笔记
tags: [JDK]
date: 2021-05-08 11:03:59
url: java-pass-by-value
---

在Java中，将基本数据数据类型或对象传递给一个方法，我们通常会说一个是值传递，一个是引用传递，但是真的是这样吗？

<!--more-->

其实这种说法是错误的，在Java中，所有都是严格的值传递。

## 疑问

假设有下面一串代码，两个类Foo和Dog，在Foo类的方法中，操作Dog类

```java
class Foo {
    public static void main(String[] args) {
        Dog a = new Dog();
        a.setName("狗1号");
        System.out.println(a.getName());
        new Foo().setDog(a);
        System.out.println(a.getName());
    }

    public void setDog(Dog b) {
        b = new Dog();
        b.setName("狗2号");
    }
}

class Dog {
    private String name;

    public void setName(String name) {
        this.name = name;
    }

    public String getName() {
        return this.name;
    }
}
```

上面代码的执行结果为：

```java
狗1号
狗1号
```

如果按照引用传递的说法来解释，在Foo类中的setDog方法传递的Dog dog是引用，那为什么更改了引用，改为`狗2号`，在main方法第二次打印的却还是`狗1号`呢？

## 解释

```java
Dog dog = new Dog();
dog.setName("狗1号");
```

当代码执行到这里时，我们初始化了一只名字叫做`狗1号`的Dog对象。

![初始化对象](/images/hexo/48/2021-05-08-23-16.png)

```java
new Foo().setDog(a);
```

当代码执行调用方法时，问题就在这里，这里将a变量传入到方法setDog，发生的是值传递还是引用传递呢？

**这里是值传递，这个值，指的是引用值(指针或者内存值)。**

变量a是个引用，有个引用值，指向存在内存里的对象，这样才能正确的操作`狗1号`对象。

执行setDog方法时，Java将变量a的引用值，复制一份给了变量b(值传递给了变量b)，所以变量b也可以操作`狗1号`对象。

![复制引用值副本](/images/hexo/48/2021-05-08_23-20.png)

```java
public void setDog(Dog b) {
        b = new Dog();
        b.setName("狗2号");
}
```

代码继续执行，执行方法块里的代码，注意此时有一个`b = new Dog();`的操作，此操作会将变量b的引用值改变，也就是说此时变量b和a指向的不再内存里同一个Dog对象。

![不同对象](/images/hexo/48/2021-05-08_23-21.png)

到这里也就能说明问题了，Java中是不存在引用传递的，因为如果变量b是引用传递，那b改变了引用指向的对象，那变量a也应该改变执行的对象，这样才是**引用传递**。而如果变量b是**值传递**，就可以解释，为什么在setDog()方法执行过后，变量a还是指向原来的对象。

我上面解释的可能也不是很清晰明了，大家可以看下面的网址，讲的更清楚。

> * [stackoverflow](https://stackoverflow.com/questions/40480/is-java-pass-by-reference-or-pass-by-value)
> * [Java is Pass-by-Value, Dammit!](http://www.javadude.com/articles/passbyvalue.htm)
> * [Java is Pass-by-Value, Dammit!中文翻译](https://blog.csdn.net/FIRE_TRAY/article/details/50649569)

