---
title: Dorado7系列之框架篇:2.搭建BDF3框架
date: 2019-02-12 09:27:19
tags: [Dorado7]

categories: [Dorado7]
---

# BDF3介绍

引用BDF3的GitHub的介绍:

```
bdf3基于spring-boot研发的开发框架。包含用户、角色、菜单、权限（最小粒度为组件）、数据导入、字典、日志、实时通讯、公众号、微程序、云数据库、个人中心、云数据源、用户个性化和一个或多个数据库实例的独立数据库模式的多租户功能模块。功能模块化，自动化，参考spring boot项目结构构建，提供一些列预定义依赖项目快。基于bdf3快速开发企业管理系统。
```

<!--more-->

分享一下[BDF3的GitHub地址](https://github.com/muxiangqiu/bdf3)。

我们在BDF3的GitHub地址的页面上找到对应的示例下载，然后使用Maven导入到Eclipse中。

# BDF3目录结构

![cmd-markdown-logo](http://kyshblogs.oss-cn-beijing.aliyuncs.com/Dorado7/Framework-Paper/Dorado7-framework-0001.png)



# 配置文件说明

这个时候我们需要修改一下application.properties这个配置文件

```properties
#服务器端口设置
server.port = 8080
#项目路径
server.context-path=/bdf
#是否打印sql语句
spring.jpa.showSql=true
#hibernate反向创建表设置，update启动时更新表结构，create 启动时重新创建表结构，none 启动时不检查
spring.jpa.hibernate.ddl-auto=update
#springboot热部署设置，添加文件改动不重启目录。
spring.devtools.restart.additional-exclude=com/**
#数据库脚本的编码设置为UTF-8
spring.datasource.sql-script-encoding=UTF-8


#数据源配置，pom中需要引入对应的数据库jdbc依赖
spring.datasource.continue-on-error=true
spring.datasource.url=jdbc:mysql://localhost:3306/bdf3
spring.datasource.username=root
spring.datasource.password=root
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
#如果数据库为非嵌入式数据库，这个属性第一次启动的时候一定要设置为ALWAYS，用于初始化数据，初始化好后，可以关闭，也可以不关闭，有自己决定
spring.datasource.initialization-mode=ALWAYS
```



# 修改项目的Java为JDK

选中项目->右击在菜单中选择Properties->Java Build Path->Libraries->选中JRE->点击右侧的Edit->修改成JDK

![cmd-markdown-logo](http://kyshblogs.oss-cn-beijing.aliyuncs.com/Dorado7/Framework-Paper/Dorado7-framework-0005.png)

现在我们跑一下项目，找到Spring Boot的启动类SampleApplication选择main方法运行。

跑了一会之后发现报错：

```
Cannot load driver class: com.mysql.jdbc.Driver
无法加载驱动程序类:com.mysql.jdbc.Driver
```

我们看一下pom.xml文件

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<!-- 继承的父项目 -->
	<parent>
		<groupId>com.bstek.bdf3</groupId>
		<artifactId>bdf3-starter-parent</artifactId>
		<version>2.0.0.RELEASE</version>
	</parent>
	<artifactId>bdf3-sample</artifactId>

	<dependencies>
		<!-- bdf3预定义依赖，简化依赖的复杂度 -->
		<dependency>
			<groupId>com.bstek.bdf3</groupId>
			<artifactId>bdf3-starter</artifactId>
		</dependency>
		<!-- <dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-redis</artifactId>
		</dependency> -->
		<!-- 开发测试工具 -->
		<!-- <dependency> <groupId>org.springframework.boot</groupId> <artifactId>spring-boot-devtools</artifactId> 
			<scope>provided</scope> </dependency> -->
		<!-- 数据库驱动 -->

		<!-- <dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
		</dependency> -->
		<dependency> 
			<groupId>com.h2database</groupId>
			 <artifactId>h2</artifactId> 
		</dependency>

	</dependencies>
	<!-- bdf3项目jar存放的maven私服 -->
	<repositories>
		<repository>
			<id>bsdn-maven-repository</id>
			<url>http://nexus.bsdn.org/content/groups/public/</url>
		</repository>
	</repositories>
</project>

```

原来是数据库驱动的包没有加载，这里我们把数据库驱动的注释去除掉。重新编译一下，等包下载好之后重新运行一下。

```
 * 
 * ========================
 * 
 * WARN:
 * Dorado is currently running in debug mode, you may need to change the setting for "core.runMode".
 * 
 * ========================
 * 
```

这时看到控制台出现这个就表示我们已经顺利的启动起来了。我们去数据库查看就能发现数据库已经生成了对应的表。

```sql
bdf3_comp_cfg
bdf3_comp_memb
bdf3_component
bdf3_dictionary -------------数据字典表
bdf3_dictionary_item
bdf3_entry
bdf3_group
bdf3_group_member
bdf3_group_template
bdf3_importer_solution
bdf3_log_info -------------登录日志表
bdf3_mapping_rule
bdf3_member_notice
bdf3_notice
bdf3_permission
bdf3_profile
bdf3_role -------------角色表
bdf3_role_granted_authority
bdf3_template
bdf3_url -------------路径表
bdf3_user -------------用户表
```

我们打开游览器输入http://localhost:8080/bdf3.security.ui.view.Login.d

用户名和密码在数据库的用户表中可以找到。

登录之后发现所有的中文显示的都是?，数据库的所有的中文也是同样的情况，英文和数字符号则不受影响，则说明是数据库连接的配置出现问题。

![cmd-markdown-logo](http://kyshblogs.oss-cn-beijing.aliyuncs.com/Dorado7/Framework-Paper/Dorado7-framework-0006.png)

需要修改application.properties配置文件：

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/bdf3?characterEncoding=utf8
```

然后删除刚才错误的表数据，重新启动一遍，则恢复正常。

到这里搭建BDF3框架的基本教程就结束了。