---
title: Gradle 创建 SpringBoot 多模块项目
categories: 代码笔记
tags: [Gradle,项目管理]
date: 2020-12-06 19:19:00
url: gradle-create-spring-boot-multi-module-project
---

Gradle 是一个基于 Apache Ant 和 Apache Maven 概念的项目自动化建构工具。Gradle 构建脚本使用的是 Groovy 或 Kotlin 的特定领域语言来编写的，而不是传统的XML。

<!--more-->

## 创建 Gradle 根项目

首先创建一个基础的 Gradle 工程，这个创建步骤和创建 Maven 工程类似，在 IDEA 中，点击 File -> new Project

![创建 Gradle 工程](/images/hexo/4/20201201223825.png)

点击 Next ，依次填写 GroupId、AtifactId、Version，这三个值的含义与 Maven 里的含义一样

![填写信息](/images/hexo/4/20201201224210.png)

点击 Next，最后点击 Finish

![完成创建](/images/hexo/4/20201201224218.png)

这时，在项目里，除了src等之类目录， 还应该会有 **build.gradle** 和 **settings.gradle** 文件，注意这两个文件，下面会用到！

### 修改 build.gradle

build.gradle 文件可以理解为 Maven 里的 pom.xml 文件，修改刚刚创建的那个项目里的 build.gradle 内容，将下面内容全部替换上去

```json
subprojects {
    // 所有项目的 group
    group "cn.liaocp.tenement"
    // 所有项目的 vesion
    version "1.0"

    // 应用插件，如果使用 Eclipse 开发，下面的 idea 可改为 eclipse
    apply plugin: "java"
    apply plugin: "idea"

    // 设置 JDK 版本
    sourceCompatibility = 1.8
    targetCompatibility = 1.8

    // 仓库配置
    repositories {
        // 1、本地的 Maven 仓库
        mavenLocal()

        // 2、远程仓库地址
        maven {
            url "https://maven.aliyun.com/nexus/content/groups/public"
        }
    }

    //管理版本
    ext {
        springbootVersion = "2.4.0"
        lombokVersion = "1.18.8"
        mysqlDriverVersion = "8.0.9-rc"
    }

    //项目中所使用到的依赖，这里添加的依赖会被所有子模块继承
    dependencies {
        implementation "org.springframework.boot:spring-boot-starter-cache:$springbootVersion"
        implementation "org.springframework.boot:spring-boot-starter-data-jpa:$springbootVersion"
        implementation "org.springframework.boot:spring-boot-starter-security:$springbootVersion"
        implementation "org.springframework.boot:spring-boot-starter-validation:$springbootVersion"
        implementation "org.springframework.boot:spring-boot-starter-web:$springbootVersion"
        implementation "org.projectlombok:lombok:$lombokVersion"
        implementation "org.springframework.boot:spring-boot-devtools:$springbootVersion"
        annotationProcessor "org.projectlombok:lombok:$lombokVersion"
        runtimeOnly "mysql:mysql-connector-java:$mysqlDriverVersion"
        testImplementation "org.springframework.boot:spring-boot-starter-test:$springbootVersion"
    }

}
```

到这里，当前的项目可以看做此项目为 root 项目，子模块的所有依赖，都通过这里继承，项目里的版本号在这里控制

## 创建 core 模块

在 IDEA 的 Project 窗口，右键，选择 new -> Module，然后就会弹出一个类似上面创建 Gradle 项目的窗口

![新建模块](/images/hexo/4/20201201225639.png)

不同的是当我们点击 Next 之后

![选择模块工程](/images/hexo/4/20201201225755.png)

窗口里多了一列 Add as module to xxx，以及 GroupId 和 Version 这时是不能修改的，只能填写 AtifactId，我们填写 core 

![模块信息](/images/hexo/4/20201201225841.png)

接下来 next -> Finish 就创建好了

### 修改 build.gradle

由于在 tenement 里已经定义了很多东西，所以在 core 里去没必要重复定义了，可以删除里面的所有内容

到这里，我们以及有了一个父工程和它的子模块了，他们两个的关系是在父工程里的 settings.gradle 文件里定义的，打开名字为 tenement 里面的 settings.gradle 

```json
// Root 项目名称
rootProject.name = 'tenement'
// 子模块名称
include 'core'
```

## 创建 admin 模块

重复上面的创建模块的步骤，创建一个名称为 admin 模块，在这个模块里，我们需要创建一个 SpringBoot 的启动类，将给它作为项目的启动模块，并使用配置 Gradle 打包脚本

![包结构](/images/hexo/4/20201201233159.png)

```java
package cn.liaocp.tenement;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * 后台项目启动类
 * @author <a href="mailto:2964556627@qq.com">Liao.Chunping</a>
 */
@SpringBootApplication
public class AdminApplication extends SpringApplication {

    public static void main(String[] args) {

        SpringApplication.run(AdminApplication.class);

    }

}
```

### 修改 build.gradle

由于 admin 模块，负责启动和打包，所以里面会有一些其他的配置，例如构建脚本，启动类等，同时依赖 core 模块

将下面内容替换 admin 模块里的 build.gradle 文件

```json
// 注意，此处需要修改为下面两个插件，才能启动和打包
apply plugin:'application'
apply plugin: 'org.springframework.boot'

//启动类
mainClassName = "cn.liaocp.tenement.AdminApplication"

dependencies {
    // 加入 core 模块依赖
    compile project(":core")
}

//声明gradle脚本自身需要使用的资源
buildscript {
    //repositories为什么要要声明两次
    //
    //buildscript中的声明是gradle脚本自身需要使用的资源。可以声明的资源包括依赖项、第三方插件、maven仓库地址等。
    //
    //而在build.gradle文件中直接声明的依赖项、仓库地址等信息是项目自身需要的资源。
    repositories {
        mavenLocal()
        maven {
            url "http://maven.aliyun.com/nexus/content/groups/public/"
        }
    }

    ext {
        //这里管理版本
        springbootVersion = "2.4.0"
    }

    dependencies {
        //spring-boot插件
        classpath("org.springframework.boot:spring-boot-gradle-plugin:$springbootVersion")
    }
}
```

## 启动和打包

### 命令行启动

使用 cmd 进入到 admin 模块的目录下，由于我们引入了 org.springframework.boot 插件，所以我们可以通过下面命令启动：

```powershell
gradle bootRun
```

也可以通过启动：

```powershell
gradle run
```

### 命令行打包

>  打包之后的路径：admin/build/libs/admin.jar

```powershell
gradle build
```

### idea 启动和打包

> 打包之后的路径：admin/build/libs/admin.jar

找到点击箭头指向 Gradle ，按层次依次展开，就可以看到红框里的内容，双击即可，其实也就是 IDEA 帮我们运行了命令

![idea 启动和打包](/images/hexo/4/202012012324496.png)

这个时候的 admin.jar 包里面包含了项目启动需要的所有 jar 包，包括我们自己编写的 core 模块也打成了 jar 包，放在了里面

```po
java -jar admin.jar
```
