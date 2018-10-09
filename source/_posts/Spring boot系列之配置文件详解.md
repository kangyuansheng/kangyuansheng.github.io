---
title: Spring boot系列之配置文件详解
date: 2018-09-03 11:06:24
tags: [Spring boot]
categories: [Spring boot]
---

<!--more-->
### 通过命令行设置属性值：
```Java
//通过使用–server.port属性来设置xxx.jar应用的端口为8888
//在命令行运行时,连续的两个减号--就是对application.properties中的属性值进行赋值的标识
//所以下面代码等价与server.port=8080
java -jar xxx.jar --server.port=8080
```
屏蔽命令行访问属性的设置  SpringApplication.setAddCommandLineProperties(false)

### 多环境配置
在Spring Boot中多环境配置文件名需要满足application-{profile}.properties的格式，其中{profile}对应你的环境标识，比如：
>* application-dev.properties：开发环境
>* application-test.properties：测试环境
>* application-prod.properties：生产环境

```Java
## 多环境配置文件激活属性,默认以dev环境设置
spring.profiles.active=dev
//执行java -jar xxx.jar，当前为默认的开发环境（dev）
//执行java -jar xxx.jar --spring.profiles.active=test，当前为测试环境的配置（test）
//执行java -jar xxx.jar --spring.profiles.active=prod，当前为生产环境的配置（prod）
```
