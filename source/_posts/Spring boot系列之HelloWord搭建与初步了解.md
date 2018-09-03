---
title: Spring boot系列之HelloWord搭建与初步了解
date: 2018-09-03 11:06:24
tags: [Spring boot]
categories: [Spring boot]
---

### Spring Boot的主要优点：
> * 为所有Spring开发者更快的入门
> * 开箱即用，提供各种默认配置来简化项目配置
> * 内嵌式容器简化Web项目
> * 没有冗余代码生成和XML配置的要求

<!--more-->

### 搭建HelloWord项目
首先我们在idea中创建一个新项目
![cmd-markdown-logo](http://p8x1t721u.bkt.clouddn.com/Spring_boot_hello_word_01.png)

在New Project中选择Spring Initializr
![cmd-markdown-logo](http://p8x1t721u.bkt.clouddn.com/Spring_boot_hello_word_02.png)

然后我们需要输入Group和Artifact,我们这里需要选择打包的方式:jar或者war
![cmd-markdown-logo](http://p8x1t721u.bkt.clouddn.com/Spring_boot_hello_word_03.png)

在这里，我们可以动态的选择一些我们需要在Spring Boot中用到的一些模块
![cmd-markdown-logo](http://p8x1t721u.bkt.clouddn.com/Spring_boot_hello_word_04.png)

![cmd-markdown-logo](http://p8x1t721u.bkt.clouddn.com/Spring_boot_hello_word_05.png)

### Spring Boot目录结构:
> * /src/main/java  存放项目所有源代码目录
> * /src//main/resources  存放项目所有资源文件以及配置文件目录
> * /src/test  存放测试代码目录

这个时候按照idea搭建了一个Spring Boot项目,这个时候我们发现这个项目已经自动生成了一些文件.
1.SpringbootcodebaseApplication  该类是程序的入口,类内有个main方法.
```Java
/**
 * @SpringBootApplication 声明当前类为sprinboot的入口类
 * 一个springboot项目内有且只能有一个这个注解存在
 */
@SpringBootApplication
public class SpringbootCodeBaseApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringbootCodeBaseApplication.class, args);
    }
}
```
2.application.properties  该配置文件是项目的核心配置文件
3.pom.xml  maven配置文件
```Java
<!-- 引入Web模块  -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<!-- 测试模块  -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

### 编写HelloWorld:
创建HelloController类
```Java
/**
 * @RestController:
 * Spring4之后新加入的注解，原来返回json需要@ResponseBody和@Controller配合。
 * 即@RestController是@ResponseBody和@Controller的组合注解。
 */
@RestController
public class HelloController {

    /**
     * @RequestMapping 配置url映射
     * @RequestMapping此注解即可以作用在控制器的某个方法上，也可以作用在此控制器类上。
     * 当控制器在类级别上添加@RequestMapping注解时，这个注解会应用到控制器的所有处理器方法上。
     * 处理器方法上的@RequestMapping注解会对类级别上的@RequestMapping的声明进行补充。
     */
    @RequestMapping("/hello")
    public String index() {
        return "Hello World";
    }
}

```
启动主程序，打开浏览器访问http://localhost:8080/hello，可以看到页面输出Hello World