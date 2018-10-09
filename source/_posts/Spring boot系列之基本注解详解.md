---
title: Spring boot系列之基本注解详解
date: 2018-09-03 11:06:24
tags: [Spring boot]
categories: [Spring boot]
---


### 1. 注解(annotations)列表
> * @SpringBootApplication：包含了@ComponentScan、@Configuration和@EnableAutoConfiguration注解。其中 @ComponentScan让spring Boot扫描到Configuration类并把它加入到程序上下文。
> * @Configuration 等同于spring的XML配置文件；使用Java代码可以检查类型安全。
> * @EnableAutoConfiguration 自动配置。
> * @ComponentScan 组件扫描，可自动发现和装配一些Bean。
> * @Component可配合CommandLineRunner使用，在程序启动后执行一些基础任务。
> * @RestController注解是@Controller和@ResponseBody的合集,表示这是个控制器bean,并且是将函数的返回值直 接填入HTTP响应体中,是REST风格的控制器。
> * @Autowired自动导入。
> * @PathVariable获取参数。
> * @JsonBackReference解决嵌套外链问题。
> * @RepositoryRestResourcepublic配合spring-boot-starter-data-rest使用。

<!--more-->

### 2. 注解(annotations)详解
#### 2.1 @SpringBootApplication：
声明当前类为sprinboot的入口类,一个springboot项目内有且只能有一个这个注解存在,这个配置等同于：@Configuration ，@EnableAutoConfiguration 和 @ComponentScan 三个配置。

#### 2.2 @ResponseBody：
表示该方法的返回结果直接写入HTTP response body中，一般在异步获取数据时使用，用于构建RESTful的api。在使用@RequestMapping后，返回值通常解析为跳转路径，加上@ResponseBody后返回结果不会被解析为跳转路径，而是直接写入HTTP response body中。比如异步获取json数据，加上@Responsebody后，会直接返回json数据。该注解一般会配合@RequestMapping一起使用。

#### 2.3 @Controller：
用于定义控制器类，在spring项目中由控制器负责将用户发来的URL请求转发到对应的服务接口（service层），一般这个注解在类中，通常方法需要配合注解@RequestMapping
```java
@Controller
@RequestMapping(value = "/users")
public class UserController {
    ......
}
```

#### 2.4 @RestController：
用于标注控制层组件，@ResponseBody和@Controller的合集

#### 2.5 @RequestMapping：
##### 2.5.1 基础用法
@RequestMapping：配置url映射;
此注解即可以作用在控制器的某个方法上，也可以作用在此控制器类上。
当控制器在类级别上添加@RequestMapping注解时，这个注解会应用到控制器的所有处理器方法上。处理器方法上的@RequestMapping注解会对类级别上的@RequestMapping的声明进行补充。
| 属性名        | 作用   |
| --------   | -----:  |
| params     | 指定request中必须包含某些参数值是，才让该方法处理。 |
| headers        |  指定request中必须包含某些指定的header值，才能让该方法处理请求。   |
| value        |    指定请求的实际地址，指定的地址可以是URI Template 模式    |
| method        |    指定请求的method类型， GET、POST、PUT、DELETE等    |
| consumes        |    指定处理请求的提交内容类型（Content-Type），如application/json,text/html;    |
| produces        |    指定返回的内容类型，仅当request请求头中的(Accept)类型中包含该指定类型才返回    |
```java
@RequestMapping("/hello")
public String index() {
    return "Hello World";
}
```

##### 2.5.2 @RequestMapping 来处理多个 URI 
可以将多个请求映射到一个方法上去，只需要添加一个带有请求路径值列表的 @RequestMapping 注解就可以了.
```java
@RestController  
@RequestMapping("/home")  
public class IndexController {  
    @RequestMapping(value = {  
        "",  
        "/page",  
        "page*",  
        "view/*,**/msg"  
    })  
    String indexMultipleMapping() {  
        return "Hello";  
    }  
}  
```

##### 2.5.3 带有 @RequestParam 的 @RequestMapping 
@RequestParam 注解配合 @RequestMapping 一起使用，可以将请求的参数同处理方法的参数绑定在一起。
@RequestParam 注解使用的时候可以有一个值，也可以没有值。
```java
@RestController  
@RequestMapping("/home")  
public class IndexController {  
  
    //id 这个请求参数被映射到了 thegetIdByValue() 这个处理方法的参数 personId 上
    @RequestMapping(value = "/id")  
    String getIdByValue(@RequestParam("id") String personId) {  
        System.out.println("ID is " + personId);  
        return "Get ID from query string of URL with value element";  
    }  
    
    //如果请求参数和处理方法参数的名称一样的话，@RequestParam 注解的 value 这个参数就可省掉了
    @RequestMapping(value = "/personId")  
    String getId(@RequestParam String personId) {  
        System.out.println("ID is " + personId);  
        return "Get ID from query string of URL without value element";  
    }  
    
    //@RequestParam 注解的 required 这个参数定义了参数值是否是必须要传的
    @RequestMapping(value = "/name")  
    String getName(@RequestParam(value = "person", required = false) String personName) {  
        return "Required element of request param";  
    } 
}  
```

##### 2.5.4 用 @RequestMapping 处理 HTTP 的各种方法
所有的请求默认都会是 HTTP GET 类型的
```java
@RestController  
@RequestMapping("/home")  
public class IndexController {  
    //@RequestMapping 注解中的 method 元素声明了 HTTP 请求的 HTTP 方法的类型
    @RequestMapping(method = RequestMethod.GET)  
    String get() {  
        return "Hello from get";  
    }  
    @RequestMapping(method = RequestMethod.DELETE)  
    String delete() {  
        return "Hello from delete";  
    }  
    @RequestMapping(method = RequestMethod.POST)  
    String post() {  
        return "Hello from post";  
    }  
    @RequestMapping(method = RequestMethod.PUT)  
    String put() {  
        return "Hello from put";  
    }  
    @RequestMapping(method = RequestMethod.PATCH)  
    String patch() {  
        return "Hello from patch";  
    }  
}   
```

##### 2.5.5 @RequestMapping 快捷方式
Spring 4.3 引入了方法级注解的变体，也被叫做 @RequestMapping 的组合注解
方法级别的注解变体有如下几个： 
@GetMapping
@PostMapping
@PutMapping
@DeleteMapping
@PatchMapping

#### 2.6 @Autowired：
自动导入依赖的bean

#### 2.7 @Component：
泛指组件，当组件不好归类的时候，我们可以使用这个注解进行标注

#### 2.8 @Service：
一般用于修饰service层的组件

#### 2.9 @Bean：
用@Bean标注方法等价于XML中配置的bean。

#### 2.10 @Value
注入Spring boot application.properties配置的属性的值
```java
@Value("${com.didispace.configure.title}")
private String key;
```

#### 2.11 @RequestParam
a.GET和POST请求传的参数会自动转换赋值到@RequestParam 所注解的变量上
b.用来处理Content-Type: 为 application/x-www-form-urlencoded编码的内容。提交方式为get或post。（Http协议中，如果不指定Content-Type，则默认传递的参数就是application/x-www-form-urlencoded类型）
c.RequestParam实质是将Request.getParameter() 中的Key-Value参数Map利用Spring的转化机制ConversionService配置，转化成参数接收对象或字段。

1).get请求：
```java
@RequestMapping(value="/xxx", method = RequestMethod.GET)
public String xxx(@RequestParam(value="title") String title){
        return "hello";
}
```
等价于：
```java
@RequestMapping(value="/xxx", method = RequestMethod.GET)
public String xxx(HttpServletRequest request){
    String title = request.getParameter("title");
    System.out.println("title: " + title);
    return "hello";
}
```
也可以不使用@RequestParam，直接接收，此时要求controller方法中的参数名称要跟form中name名称一致
```java
@RequestMapping(value="/xxx", method = RequestMethod.GET)
public String xxx(String title){
    System.out.println("title: " + title);
    return "hello";
}
```
2).post请求：
跟get请求格式一样，只是把方法中的get换成post

#### 2.12 @RequestBody
@RequestBody注解可以接收json格式的数据，并将其转换成对应的数据类型。
处理HttpEntity传递过来的数据，一般用来处理非Content-Type: application/x-www-form-urlencoded编码格式的数据。

GET请求中，因为没有HttpEntity，所以@RequestBody并不适用。
POST请求中，通过HttpEntity传递的参数，必须要在请求头中声明数据的类型Content-Type，SpringMVC通过使用HandlerAdapter 配置的HttpMessageConverters来解析HttpEntity中的数据，然后绑定到相应的bean上

#### 2.13 @ModelAttribute
@ModelAttribute注解类型将参数绑定到Model对象

当前台界面使用GET或POST方式提交数据时，数据编码格式由请求头的ContentType指定。分为以下几种情况：
1. application/x-www-form-urlencoded，这种情况的数据@RequestParam、@ModelAttribute可以处理，@RequestBody也可以处理。
2. multipart/form-data，@RequestBody不能处理这种格式的数据。（form表单里面有文件上传时，必须要指定enctype属性值为multipart/form-data，意思是以二进制流的形式传输文件。）
3. application/json、application/xml等格式的数据，必须使用@RequestBody来处理。