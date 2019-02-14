---
title: Dorado7系列之框架篇:1.如何搭建BDF3框架
date: 2019-02-12 09:26:19
tags: [Dorado7]

categories: [Dorado7]
---

# Eclipse配置

## 修改Eclipse字体大小

[window]->[preferences]->[General]->[Appearance]->[Colors and Fonts]->[Basic]->点击展开Basic,最下面有 Text Font 这个选项，点击，然后修改保存即可。

![cmd-markdown-logo](http://kyshblogs.oss-cn-beijing.aliyuncs.com/Dorado7/Framework-Paper/Dorado7-framework-0004.png)

## 配置Dorado7开发插件

首先我们需要在官方网站上下载一个最新版的Eclipse。

在Eclipse的安装文件目录中找到dropins，将Dorado7开发插件的文件夹拷贝到其中。

**注意：**

假如用Eclipse配置完Dorado7开发插件之后，打开View视图出现**An error has occurred,See error log for more details**报错。

解决方法：在"开始"-->"运行"---->打入"cmd"进入命令提示行后,再进入eclipse的安装目录,
         然后输入eclipse -clean即可（我用的这个方法 成功了）

## 配置Jetty插件

[Help]->[Eclipse Marketplace]->在搜索条中输入Jetty点击搜索，找到Jetty插件点击**Install**，然后把插件安装下来就好了。(Ps:下载慢的小伙伴可以需要一个科学上网的工具。)

## 配置JRebel插件

我们安装完Jetty插件之后还需要安装一个插件JRebel：可快速实现热部署，节省了大量重启时间，提高了个人开发效率。

当下载完成之后需要激活一下才能使用。

## 关于Eclipse性能优化

### 减少Eclipse启动后自动启动的插件

具体操作: 在Preferences -> General -> StartUp and Shutdown: 将除Plug-ins activated on startup以外的项目有节选的去掉（比如Mylyn等没用到,就去掉了）
效果: 启动Eclipse后,会有Initialing Java Tools的滚动条,会发现快了很多.

### 关掉自动编译

具体操作: Project -> Build Automatically
效果: 在代码修改保存后,不会启动自动编译

### 减少编译需要验证的项目,提升编译速度

具体操作: 在Preferences -> Validataion 将无关的Validator去掉, 比如: 我就将和我无关的JPA, JSP, WS 都去掉了
效果: 编译项目时,Eclipse跑的Validator项目少了, 确实快了

### 配置eclipse.ini

```
-XX:MaxPermSize=768m
-Xms1024m
-Xmx3072m
```

# 数据库配置

这里我们介绍一个软件:XAMPP（Apache+MySQL+PHP+PERL）是一个功能强大的建站集成软件包。

![cmd-markdown-logo](http://kyshblogs.oss-cn-beijing.aliyuncs.com/Dorado7/Framework-Paper/Dorado7-framework-0002.png)

这里我们搭建一个bdf3数据库。

![cmd-markdown-logo](http://kyshblogs.oss-cn-beijing.aliyuncs.com/Dorado7/Framework-Paper/Dorado7-framework-0003.png)



