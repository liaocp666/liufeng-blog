---
title: 桥接模式
categories: 代码笔记
tags: [结构型设计模式]
date: 2021-03-22 22:19:44
url: bridge-design-pattern
---

桥接模式(Bridge pattern)：是一种结构型设计模式；基于类的最小设计原则；将实现和抽象放在两个不同类的层次中，使两个层次可以独立改变。

<!--more-->

其实，我所理解的桥接模式，就是用另一个维度去分类事物，然后两种维度之间，通过聚合来联系在一起。

## 问题

桥接模式，主要是解决在某种场景下，导致类爆炸的情况

## 场景

例如我们想要描述动物，以及动物的颜色。

![动物维度](/images/hexo/27/20210323101637.png)

如果将上面的图片通过代码实现，就是顶层 Animal 类或者接口，然后依次去继承或实现，最终到 黑/白猫 或 黑/白狗 类。当我们新加一个动物：虎，就需对应增加 [颜色]虎 之类的类。

### 换个维度

增加一个颜色维度来描述看看：

![颜色维度](/images/hexo/27/20210323105509.png)

将第三层的颜色，单独抽出一个维度，颜色维度。

**那我们拥有两个维度了，一个是动物，一个颜色，当我们想要具体的 [动物] + [颜色]，只需要在动物类中聚合（引用）一下颜色类即可，而引用的颜色的动物类，应该是最顶层的动物类 Animal。**

在 Animal 中，引用颜色维度，那么 Animal 就相当于是一座桥梁，将两个维度桥接在一起。

## 代码

### 动物维度

**Animal**

```Java
public abstract class Animal {

    // 聚合颜色维度
    private Color color;

    // 通过构造方法，初始化颜色维度
    public Animal(Color color) {
        this.color = color;
    }

    /**
     * 显示动物的颜色
     */
    protected void displayColor() {
        color.display();
    }

    /**
     * 动物叫
     */
    protected abstract void cry();

}
```

**Cat**

```java
public class Cat extends Animal{

    public Cat(Color color) {
        super(color);
    }

    protected void cry() {
        System.out.print("Cat -> ");
        super.displayColor();
    }
}
```

### 颜色维度

**Color**

```java
public abstract class Color {

    /**
     * 显示的颜色
     */
    public abstract void display();

}
```

**Black**

```java
public class Black extends Color {

    @Override
    public void display() {
        System.out.print("Black");
    }
}
```

### 测试

**Test**

```java
public class Test {

    public static void main(String[] args) {

        Animal animal = new Cat(new Block());
        animal.cry();

    }

}
```

**输出结果**

```java
Cat -> Black
```

