---
title: Spring Boot系列之使用篇:7.配置文件
date: 2019-03-24 01:39:00
author: 康源晟
top: false
cover: true
toc: false
mathjax: false
summary: 介绍一下Spring Boot的配置文件的使用方法
categories: Spring Boot
tags:
  - Spring Boot
---

### 1.application.properties介绍：

Spring Boot使用“习惯优于配置”的理念让你的项目快速运行起来。application.properties是Spring Boot主要的全局配置文件。一般放在src/main/resources目录下。



### 2.application.properties常用配置:

#### 2.1 数据库连接配置

```properties
#描述数据源
spring.datasource.url=jdbc:mysql://localhost:3306/demo
spring.datasource.username=root
spring.datasource.password=0000
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
```

#### 2.2 配置端口和项目名访问

```properties
#指定springboot内嵌容器启动的端口，默认使用tomcat容器时在8080端口    
server.port=8081
#配置项目访问路径
server.servlet.context-path=/boot
```

#### 2.3 日志配置

```properties
# 日志配置文件的位置。 例如对于Logback的`classpath：logback.xml`
logging.config= 
# ％wEx#记录异常时使用的转换字。
logging.exception-conversion-word= 
# 日志文件名。 例如`myapp.log`
logging.file= 
# 日志级别严重性映射。 例如`logging.level.org.springframework =  DEBUG`
logging.level.*= 
# 日志文件的位置。 例如`/ var / log`
logging.path= 
# 用于输出到控制台的Appender模式。 只支持默认的logback设置。
logging.pattern.console= 
# 用于输出到文件的Appender模式。 只支持默认的logback设置。
logging.pattern.file= 
# 日志级别的Appender模式（默认％5p）。 只支持默认的logback设置。
logging.pattern.level= 
#注册日志记录系统的初始化挂钩。
logging.register-shutdown-hook= false
```

#### 2.4 AOP 切面

```properties
# 添加@EnableAspectJAutoProxy。
spring.aop.auto= true
# 是否要创建基于子类（CGLIB）的代理（true），而不是基于标准的基于Java接口的代理（false）。
spring.aop.proxy-target-class= false


# 应用程序上下文初始化器
# 应用指标。
spring.application.index= 
# 应用程序名称。
spring.application.name= 
```

#### 2.5 国际化（消息源自动配置）

```properties
#
spring.messages.basename= messages
# 以逗号分隔的基础名称列表，每个都在ResourceBundle约定之后。
# 加载的资源束文件缓存到期，以秒为单位。 设置为-1时，软件包将永久缓存。
spring.messages.cache-seconds= -1
# 消息编码。
spring.messages.encoding= UTF-8
# 设置是否返回到系统区域设置，如果没有找到特定语言环境的文件。
spring.messages.fallback-to-system-locale= true
```

#### 2.6 Spring 核心配置

```properties
# 跳过搜索BeanInfo类。
spring.beaninfo.ignore= true

# spring 缓存配置
# 由底层缓存管理器支持的要创建的缓存名称的逗号分隔列表。
spring.cache.cache-names= 
# 用于初始化EhCache的配置文件的位置。
spring.cache.ehcache.config= 
# 用于创建缓存的规范。 检查CacheBuilderSpec有关规格格式的更多细节。
spring.cache.guava.spec= 
# 用于初始化Hazelcast的配置文件的位置。
spring.cache.hazelcast.config= 
# 用于初始化Infinispan的配置文件的位置。
spring.cache.infinispan.config= 
# 用于初始化缓存管理器的配置文件的位置。
spring.cache.jcache.config= 
# 用于检索符合JSR-107的缓存管理器的CachingProvider实现的完全限定名称。 只有在类路径上有多个JSR-107实现可用时才需要。
spring.cache.jcache.provider= 
# 缓存类型，默认情况下根据环境自动检测。
spring.cache.type= 
```

#### 2.7 WEB属性

```properties
# 文件上传属性
# 启用对文件上传的支持。
multipart.enabled= true
# 将文件写入磁盘后的阈值。 值可以使用后缀“MB”或“KB”表示兆字节或千字节大小。
multipart.file-size-threshold= 0
# 上传文件的位置。
multipart.location= 
# 最大文件大小。 值可以使用后缀“MB”或“KB”表示兆字节或千字节大小。
multipart.max-file-size= 1Mb
# 最大请求大小。 值可以使用后缀“MB”或“KB”表示兆字节或千字节大小。
multipart.max-request-size= 10Mb
```

#### 2.8 服务器属性

```properties
# 服务器应绑定到的网络地址。
server.address= 
# 如果启用响应压缩。
server.compression.enabled= false
# 从压缩中排除的用户代理列表。
server.compression.excluded-user-agents= 
# 应该压缩的MIME类型的逗号分隔列表。 例如`text / html，text / css，application / json`
server.compression.mime-types= 
# 执行压缩所需的最小响应大小。 例如2048
server.compression.min-response-size= 
# Servlet上下文初始化参数。 例如`server.context-parameters.a =  alpha`
server.context-parameters.*= 
# 应用程序的上下文路径。
server.context-path= 
# 显示应用程序的名称。
server.display-name= application
# 何时包含“stacktrace”属性。
server.error.include-stacktrace= never
# 错误控制器的路径。
server.error.path= /error
# 启动浏览器中出现服务器错误时显示的默认错误页面。
server.error.whitelabel.enabled= true
# JSP servlet的类名。
server.jsp-servlet.class-name= org.apache.jasper.servlet.JspServlet
# Init参数用于配置JSP servlet
server.jsp-servlet.init-parameters.*= 
# JSP servlet是否被注册
server.jsp-servlet.registered= true
# 服务器HTTP端口。
server.port= 8080
# 主调度程序servlet的路径。
server.servlet-path= /
# 会话cookie的注释。
server.session.cookie.comment= 
# 会话cookie的域。
server.session.cookie.domain= 
# “HttpOnly”标志为会话cookie。
server.session.cookie.http-only= 
# 会话cookie的最大时长（以秒为单位）。
server.session.cookie.max-age= 
# 会话cookie名称。
server.session.cookie.name= 
# 会话cookie的路径。
server.session.cookie.path= 
# 会话cookie的“安全”标志。
server.session.cookie.secure= 
# 重启之间持续会话数据。
server.session.persistent= false
# 用于存储会话数据的目录。
server.session.store-dir= 
# 会话超时（秒）。
server.session.timeout= 
# 会话跟踪模式（以下一个或多个：“cookie”，“url”，“ssl”）。
server.session.tracking-modes= 
# 支持SSL密码。
server.ssl.ciphers= 
# 客户端认证是否需要（“want”）或需要（“need”）。 需要信任存储。
server.ssl.client-auth= 
# ssl配置
server.ssl.enabled= 
server.ssl.key-alias= 
server.ssl.key-password= 
server.ssl.key-store= 
server.ssl.key-store-password= 
server.ssl.key-store-provider= 
server.ssl.key-store-type= 
server.ssl.protocol= 
server.ssl.trust-store= 
server.ssl.trust-store-password= 
server.ssl.trust-store-provider= 
server.ssl.trust-store-type= 
# 创建日志文件的目录。 可以相对于tomcat base dir或absolute。
server.tomcat.accesslog.directory= 
# 启用访问日志。
server.tomcat.accesslog.enabled= false
# 访问日志的格式化模式。
server.tomcat.accesslog.pattern= common
# 日志文件名前缀。
server.tomcat.accesslog.prefix= access_log
# 日志文件名后缀。
server.tomcat.accesslog.suffix= .log
# 在调用backgroundProcess方法之间延迟秒。
server.tomcat.background-processor-delay= 30
# Tomcat基本目录。 如果未指定，将使用临时目录。
server.tomcat.basedir= 
# 正则表达式匹配可信IP地址。
server.tomcat.internal-proxies= 10\\.\\d{1,3}\\.\\d{1,3}\\.\\d{1,3}|\\192\\.168\\.\\d{1,3}\\.\\d{1,3}|\\169\\.254\\.\\d{1,3}\\.\\d{1,3}|\\127\\.\\d{1,3}\\.\\d{1,3}\\.\\d{1,3}|\\172\\.1[6-9]{1}\\.\\d{1,3}\\.\\d{1,3}|\\172\\.2[0-9]{1}\\.\\d{1,3}\\.\\d{1,3}|\\172\\.3[0-1]{1}\\.\\d{1,3}\\.\\d{1,3}
# HTTP消息头的最大大小（以字节为单位）。
server.tomcat.max-http-header-size= 0
# 最大工作线程数。
server.tomcat.max-threads= 0
# 用于覆盖原始端口值的HTTP头的名称。
server.tomcat.port-header= X-Forwarded-Port
# 头文件，保存传入协议，通常命名为“X-Forwarded-Proto”。
server.tomcat.protocol-header= 
# 表示传入请求使用SSL的协议头的值。
server.tomcat.protocol-header-https-value= https
# 提取远程ip的HTTP头的名称。 例如`X-FORWARDED-FOR`
server.tomcat.remote-ip-header= 
# 用于解码URI的字符编码。
server.tomcat.uri-encoding= UTF-8
# 访问日志目录。
server.undertow.accesslog.dir= 
# 启用访问日志。
server.undertow.accesslog.enabled= false
# 访问日志的格式化模式。
server.undertow.accesslog.pattern= common
# 每个缓冲区的大小字节数。
server.undertow.buffer-size= 
# 每个区域的缓冲区数。
server.undertow.buffers-per-region= 
# 在Java堆之外分配缓冲区。
server.undertow.direct-buffers= 
# 为工作者创建的I / O线程数。
server.undertow.io-threads= 
# 工作线程数。
server.undertow.worker-threads= 
# 如果X-Forwarded- *头应该应用于HttpRequest。
server.use-forward-headers= 
```



### 3 自定义属性与加载

#### 3.1 自定义属性

我们在使用Spring Boot的时候，通常也需要定义一些自己使用的属性，我们可以如下方式直接定义：

```properties
#自定义配置属性
com.example.demo.property1=text
com.example.demo.property2=1
com.example.demo.property3=true
```

然后通过`@Value("${属性名}")`注解来加载对应的配置属性，具体如下：

```java
@Component
@Data
public class TestProperties {

    @Value("${com.example.demo.property1}")
    private String property1;

    @Value("${com.example.demo.property2}")
    private int property2;

    @Value("${com.example.demo.property3}")
    private boolean property3;
}

```

然后我们来测试一下。

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest()
public class ApplicationTests {

    @Autowired
    private TestProperties testProperties;


    @Test
    public void getHello() throws Exception {
        Assert.assertEquals(testProperties.getProperty1(), "text");
        Assert.assertEquals(testProperties.getProperty2(), 1);
        Assert.assertEquals(testProperties.isProperty3(), true);
    }
}
```



#### 3.2 参数间的引用

在`application.properties`中的各个参数之间也可以直接引用来使用，就像下面的设置：

```properties
com.example.demo.property4=${com.example.demo.property1}+${com.example.demo.property2}
```



#### 3.3 使用随机数

有时候对于一些特殊的属性的时候，我们并不希望它的参数值是一个固定的值。这样的话我们就需要使用到随机数的概念。

```properties
# 随机字符串
com.example.demo.random.value=${random.value}
# 随机int
com.example.demo.random.number=${random.int}
# 随机long
com.example.demo.random.bignumber=${random.long}
# 10以内的随机数
com.example.demo.random.test1=${random.int(10)}
# 10-20的随机数
com.example.demo.random.test2=${random.int[10,20]}
```



#### 3.4 通过命令行设置属性值

在命令行运行时，连续的两个减号`--`就是对`application.properties`中的属性值进行赋值的标识。所以，`java -jar xxx.jar --server.port=8888`命令，等价于我们在`application.properties`中添加属性`server.port=8888`，该设置在样例工程中可见，读者可通过删除该值或使用命令行来设置该值来验证。

通过命令行来修改属性值固然提供了不错的便利性，但是通过命令行就能更改应用运行的参数，那岂不是很不安全？是的，所以Spring Boot也贴心的提供了屏蔽命令行访问属性的设置，只需要这句设置就能屏蔽：`SpringApplication.setAddCommandLineProperties(false)`。



#### 3.5 多环境配置

我们在开发Spring Boot的时候会发现，我们针对多个环境需要配置多种配置文件。比如：开发、测试、生产等。

在Spring Boot中多环境配置文件名需要满足`application-{profile}.properties`的格式，其中`{profile}`对应你的环境标识，比如：

- `application-dev.properties`：开发环境
- `application-test.properties`：测试环境
- `application-prod.properties`：生产环境

至于哪个具体的配置文件会被加载，需要在`application.properties`文件中通过`spring.profiles.active`属性来设置，其值对应`{profile}`值。

如：`spring.profiles.active=test`就会加载`application-test.properties`配置文件内容



#### 3.6 @ConfigurationProperties

手动书写@Value还是比较繁重的工作，好在Spring Boot提供了更简洁的方式。@ConfigurationProperties(prefix = "test")。prefix指定了配置文件的前缀为test，并且按照属性名进行自动匹配，例如：test.name属性值会自动加载到private String name域中。

Spring Boot1.5之前的写法:

```java
@Component
@ConfigurationProperties(prefix = "test", locations = "classpath:xxxx.properties")
public class Configuration {

    private String name;

    private String age;

    private Long tel;

    // setter getter
}
```

