---
title: Spring Boot系列之使用篇:1.HelloWord搭建与初步了解
date: 2018-09-14 09:25:00
author: 康源晟
top: false
cover: true
toc: false
mathjax: false
summary: HelloWord搭建与初步了解
categories: Spring Boot
tags:
  - Spring Boot
---

### 1.Spring Boot的介绍：

Spring最重要的是以下四个核心。

- 自动配置：针对很多Spring应用程序常见的应用功能，Spring Boot能自动提供相关配置
- 起步依赖：告诉Spring Boot需要什么功能，它就能引入需要的库。
- 命令行界面：这是Spring Boot的可选特性，借此你只需写代码就能完成完整的应用程序，无需传统项目构建。
- Actuator：让你能够深入运行中的Spring Boot应用程序，一探究竟。



### 2.搭建HelloWord项目

目前构建项目有多种方式我这里简单介绍两种。

- 使用spring官网 SPRING INITIALIZR 来构建项目结构（地址：<http://start.spring.io/>）
-  使用IntelliJ IDEA开发工具来构建项目结构

#### 2.1 使用Spring官网 SPRING INITIALIZR 来构建项目结构

![](http://kyshblogs.oss-cn-beijing.aliyuncs.com/Spring-Boot/Spring-Boot-0001.png)

点击Generate Project就会下载一个根据你配置生成的压缩文件，然后进行解压。

打开idea导入刚下载的项目，然后一直下一步就可以了。导入之后可以需要下载一段时间的jar包，不要急稍微等待一会。

![](http://kyshblogs.oss-cn-beijing.aliyuncs.com/Spring-Boot/Spring-Boot-0002.png)



#### 2.2 使用IntelliJ IDEA开发工具来构建项目结构

首先我们打开idea选择创建新项目。

![](http://kyshblogs.oss-cn-beijing.aliyuncs.com/Spring-Boot/Spring-Boot-0003.png)

在打开的界面中选择Spring Initializr->然后选择Next即可

![](http://kyshblogs.oss-cn-beijing.aliyuncs.com/Spring-Boot/Spring-Boot-0004.png)

在该界面中填写项目对应配置。

![](http://kyshblogs.oss-cn-beijing.aliyuncs.com/Spring-Boot/Spring-Boot-0005.png)

根据需要下载对应的模块。



### 3.Spring Boot目录结构:

> * /src/main/java  存放项目所有源代码目录
> * /src//main/resources  存放项目所有资源文件以及配置文件目录
> * /src/test  存放测试代码目录

这个时候按照idea搭建了一个Spring Boot项目,这个时候我们发现这个项目已经自动生成了一些文件.

#### 3.1 SpringbootcodebaseApplication

该类是程序的入口,类内有个main方法.

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
首先我们看一下启动注解@SpringBootApplication的源码：

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = {
		@Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
	......
}
```

这个时候我们就发现`@SpringBootApplication`是一个复合注解，主要包括了`@ComponentScan`，和`@SpringBootConfiguration`，`@EnableAutoConfiguration`。

- `@SpringBootConfiguration`继承自`@Configuration`，二者功能也一致，标注当前类是配置类，并会将当前类内声明的一个或多个以`@Bean`注解标记的方法的实例纳入到`srping`容器中，并且实例名就是方法名。
- `@EnableAutoConfiguration`是springboot实现自动化配置的核心注解，通过这个注解把spring应用所需的bean注入容器中．`@EnableAutoConfiguration`源码通过`@Import`注入了一个`ImportSelector`的实现类
   `AutoConfigurationImportSelector`,这个`ImportSelector`最终实现根据我们的配置，动态加载所需的bean。
- `@ComponentScan`，扫描当前包及其子包下被`@Component`，`@Controller`，`@Service`，`@Repository`注解标记的类并纳入到spring容器中进行管理。是以前的`<context:component-scan>`（以前使用在xml中使用的标签，用来扫描包配置的平行支持）。所以本demo中的User为何会被`spring`容器管理。

`@SpringBootApplication`只会扫描`@SpringBootApplication`注解标记类包下及其子包的类（特定注解标记，比如说`@Controller`，`@Service`，`@Component`,`@Configuration`和`@Bean`注解等等）纳入到spring容器。

假如出现与之不在同一个包下的类，则需要指定扫描路径：`@SpringBootApplication(scanBasePackages = "xxx.xxx.xxx")`

不让某个类扫描到则需要添加`exclude或excludeName`

```java
@SpringBootApplication(exclude = xxx.class)
@SpringBootApplication(excludeName = {"xxx.xx.xxx"})
```



#### 3.2 application.properties

该配置文件是项目的核心配置文件

#### 3.3 pom.xml  maven配置文件

```xml
<!-- 引入Web模块  -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
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

