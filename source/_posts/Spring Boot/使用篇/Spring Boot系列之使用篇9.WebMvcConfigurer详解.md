---
title: Spring Boot系列之使用篇:9.WebMvcConfigurer详解
date: 2019-03-24 23:44:00
author: 康源晟
top: false
cover: true
toc: false
mathjax: false
summary: WebMvcConfigurer详解
categories: Spring Boot
tags:
  - Spring Boot

---

### 1.WebMvcConfigurer 介绍

被用来定制 Spring Boot 全局 MVC 属性，通常我们需要定义一个实现了 `WebMvcConfigurer` 接口配置类，然后在此类中配置相关 MVC 属性。

**以下WebMvcConfigurerAdapter 比较常用的重写接口**

```java
/** 解决跨域问题 **/
public void addCorsMappings(CorsRegistry registry) ;
/** 添加拦截器 **/
void addInterceptors(InterceptorRegistry registry);
/** 这里配置视图解析器 **/
void configureViewResolvers(ViewResolverRegistry registry);
/** 配置内容裁决的一些选项 **/
void configureContentNegotiation(ContentNegotiationConfigurer configurer);
/** 视图跳转控制器 **/
void addViewControllers(ViewControllerRegistry registry);
/** 静态资源处理 **/
void addResourceHandlers(ResourceHandlerRegistry registry);
/** 默认静态资源处理器 **/
void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer);
```



### 2.跨域支持

Spring Boot 提供了对 CORS 的支持，您可以实现 `addCorsMappings` 接口来添加规则来允许跨域访问：

```java
@Override
    public void addCorsMappings(CorsRegistry registry) {
        // 允许所有跨域访问
        registry.addMapping("/**");
        // 更为精细的控制
        registry.addMapping("/**")
                .allowedOrigins("*")
                .allowCredentials(true)
                .allowedMethods("GET", "POST", "DELETE", "PUT")
                .maxAge(3600 * 24);
    }
```

#### 2.1 同源策略

同源策略是浏览器上为安全性考虑实施的非常重要的安全策略。

同源：URL由协议、域名、端口和路径组成，如果两个URL的协议、域名和端口相同，则表示他们同源。

同源策略：浏览器的同源策略，限制了来自不同源的"document"或脚本，对当前"document"读取或设置某些属性。从一个域上加载的脚本不允许访问另外一个域的文档属性。



### 3.配置日期格式化

```java
/**
* 配置日期格式化
* @param registry
*/
@Override
public void addFormatters(FormatterRegistry registry) {
	registry.addFormatter(new DateFormatter("yyyy-MM-dd HH:mm:ss"));
}
```



### 4.配置 Jackson

**Jackson 是 Spring Boot 内置的 Json 解析框架，用来完成出入参的序列化和反序列化**。

通常，我们会在 Controller 类中方法上，加上 `@RequestBody` 或者 `@ResponseBody` 注解，Spring Boot 会自动对出入参做 Json 解析与转换工作。

**`@RequestBody`用于将入参 Json 转换成对象，而 `@ResponseBody` 用于将对象转换成 Json 返回。**

定义一个 `JacksonConfig` 配置类：

```java
@Configuration
public class JacksonConfig {

    @Bean
    public ObjectMapper objectMapper() {
        ObjectMapper objectMapper = new ObjectMapper();
        // 自定义日期转换格式
        objectMapper.setDateFormat(new SimpleDateFormat("yyyy-MM-dd HH:mm:ss"));
        return objectMapper;
    }
}
```



### 5.配置拦截器

```java
/**
* 配置拦截器
* @param interceptorRegistry
*/
@Override
public void addInterceptors(InterceptorRegistry interceptorRegistry) {
	// 拦截所有 /admin/** 的访问地址
	interceptorRegistry.addInterceptor(new 			 LoginValidationInterceptor()).addPathPatterns("/admin/**");
}
```

