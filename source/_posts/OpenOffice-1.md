---
title: 通过OpenOffice实现文件格式转换
date: 2018-06-07 14:24:24
tags: [OpenOffice]
categories: [OpenOffice]
---

### 概述
<!--more-->
主要原理:
通过第三方工具OpenOffice实现文件格式转换

### 环境搭建
OpenOffice 下载地址http://www.openoffice.org/
加载之后安装OpenOffice

### 启动服务
#### 通过本地方式调用并且开启服务
通过cmd调用服务, "cd D:/openOffice/install/program" 
执行 soffice -headless -accept="socket,host=127.0.0.1,port=8100;urp;" -nofirststartwizard
查看是否安装成功  netstat -ano|findstr  8100
#### 通过Java方式调用并且开启服务
```Java
// 调用openoffice服务线程
String command = "C:\Program Files (x86)\OpenOffice 4\program\soffice.exe -headless -accept=\"socket,host=127.0.0.1,port=8100;urp;\"";
Process p = Runtime.getRuntime().exec(command);
// 关闭进程
p.destroy();
```

### 程序代码
#### 首先简述几个包的用途:
``` javascript
<!-- 转换核心包 -->
<dependency>
    <groupId>org.jodconverter</groupId>
    <artifactId>jodconverter-core</artifactId>
    <version>4.1.0</version>
</dependency>
<!-- 本地化部署，还有一个是 online 版，有需要自己去了解下 -->
<dependency>
    <groupId>org.jodconverter</groupId>
    <artifactId>jodconverter-local</artifactId>
    <version>4.1.0</version>
</dependency>
<!-- 与 Spring boot 依赖配置 -->
<dependency>
    <groupId>org.jodconverter</groupId>
    <artifactId>jodconverter-spring-boot-starter</artifactId>
    <version>4.1.0</version>
</dependency>
```

#### 与Spring boot框架连接下的程序调用方法
```Java
@Autowired
private DocumentConverter documentConverter;

documentConverter.convert("源文件").to("目标文件").execute();
```

#### Java方法调用1
```Java
File inputFile = new File("源文件");
File outputFile = new File("目标文件");
JodConverter
         .convert(inputFile)
         .to(outputFile)
         .execute();
```
或者
```Java
InputStream inputStream = ...
OutputStream outputStream = ...
JodConverter
         .convert(inputStream)
         .as(DefaultDocumentFormatRegistry.XLS)
         .to(outputStream)
         .as(DefaultDocumentFormatRegistry.ODS)
         .execute();
```
#### Java方法调用2
```Java
// 连接openoffice服务
OpenOfficeConnection connection = new SocketOpenOfficeConnection("127.0.0.1", 8100);
connection.connect();
// 转换word到pdf
DocumentConverter converter = new OpenOfficeDocumentConverter(connection);
converter.convert(inputFile, outputFile);
// 关闭连接
connection.disconnect();
```