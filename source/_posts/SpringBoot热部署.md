---
title: SpringBoot热部署
date: 2018-03-21 14:33:23
tags: [springboot]
---

SpringBoot热部署
---

### 原理:

```
是在发现代码有更改之后，重新启动应用，但是速度比手动停止后再启动更快。其深层原理是使
用了两个ClassLoader，一个Classloader加载那些不会改变的类(第三方Jar包),另一个ClassLoa
der加载会更改的类，称为restart ClassLoader,这样在有代码更改的时候，原来的restart
ClassLoader被丢弃，重新创建一个restart ClassLoader，由于需要加载的类相比较少，所以实现了较快的重启时间。
即devtools会监听classpath下的文件变动，并且会立即重启应用（发生在保存时机）
```

### IDEA开启SpringBoot热启动

#### 一、开启idea自动make功能

1、CTRL + SHIFT + A --> 查找make project automatically --> 选中

![mark](http://ozaomob5f.bkt.clouddn.com/images/180202/5D2jHikmkd.png?imageslim)

2、CTRL + SHIFT + A --> 查找Registry --> 找到并勾选compiler.automake.allow.when.app.running ![mark](http://ozaomob5f.bkt.clouddn.com/images/180202/ECEEA18F3I.png?imageslim)

1. 最后重启idea

### 二、使用spring-boot-1.3开始有的热部署功能

#### 1、加maven依赖

```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <optional>true</optional>
    </dependency>
```

#### 2、开启热部署

复制代码

```xml
   <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <fork>true</fork>//该配置必须
                </configuration>
            </plugin>
        </plugins>
    </build>
```