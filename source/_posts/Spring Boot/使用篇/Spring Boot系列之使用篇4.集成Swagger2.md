---
title: Spring Boot系列之使用篇:4.集成Swagger2
date: 2019-03-23 23:27:00
author: 康源晟
top: false
cover: true
toc: false
mathjax: false
summary: 集成Swagger2
categories: Spring Boot
tags:
  - Spring Boot
  - Swagger2

---

### 1.Swagger2介绍：

`Swagger`是一款`RESTful`接口的文档在线自动生成、功能测试功能框架。一个规范和完整的框架，用于生成、描述、调用和可视化`RESTful`风格的Web服务，加上`swagger-ui`，可以有很好的呈现。



### 2.在项目中引入Swagger2

#### 2.1修改pom 文件

```xml
<!-- 引入swagger2模块 -->
<dependency>
   <groupId>io.springfox</groupId>
   <artifactId>springfox-swagger2</artifactId>
   <version>2.6.1</version>
</dependency>
<dependency>
   <groupId>io.springfox</groupId>
   <artifactId>springfox-swagger-ui</artifactId>
   <version>2.6.1</version>
</dependency>
```

#### 2.2 构建swagger2配置类

```java
//注解开启 swagger2 功能
@EnableSwagger2
//注解标示,这是一个配置类,@Configuation注解包含了@Component注解
@Configuration
public class Swagger2 {

    /**
     * 通过 createRestApi函数来构建一个DocketBean
     * 函数名,可以随意命名,喜欢什么命名就什么命名
     */
    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())//调用apiInfo方法,创建一个ApiInfo实例,里面是展示在文档页面信息内容
                .select()
                //控制暴露出去的路径下的实例
                //如果某个接口不想暴露,可以使用以下注解
                //@ApiIgnore 这样,该接口就不会暴露在 swagger2 的页面下
                .apis(RequestHandlerSelectors.basePackage("com.example.demo"))
                .paths(PathSelectors.any())
                .build();
    }
    //构建 api文档的详细信息函数
    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                //页面标题
                .title("Spring Boot 测试使用 Swagger2 构建RESTful API")
                //创建人
                .contact("admin")
                //版本号
                .version("0.0.1")
                //描述
                .description("API 描述")
                .build();
    }
}
```

#### 2.3在启动类添加`@EnableSwagger2` 表示开启Swagger

```java
@EnableSwagger2
@SpringBootApplication
public class SpringBootDemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringBootDemoApplication.class, args);
    }

}

```

#### 2.4 Swagger2文档

启动SpringBoot项目，访问 <http://localhost:8080/swagger-ui.html>



### 3.Swagger2使用

#### 3.1 `@Api`  协议集描述

```java
@Api：放在 请求的类上，与@Controller并列，说明的请求类的用下，如用户登录类，订单类等。
	tags="说明该类的作用"
	value="该参数没什么意义，所以不需要配置"
    
示例：
@Api(tags="APP登录授权")
@Controller
public class ApiLoginController {
	.........
}
```

`@Api`详细属性配置：

![](http://kyshblogs.oss-cn-beijing.aliyuncs.com/Spring-Boot/Spring-Boot-0009.png)

#### 3.2 `@ApiOperation`：方法的说明

```java
@ApiOperation："用在请求的方法上，说明方法的作用"
	value="说明方法的作用"
	notes="方法的备注说明"
```

##### 3.2.1 `@ApiImplicitParams`、`@ApiImplicitParam`：方法参数的说明

```java
@ApiImplicitParams：用在请求的方法上，包含一组参数说明
	@ApiImplicitParam：对单个参数的说明	    
	    name：参数名
	    value：参数的汉字说明、解释
	    required：参数是否必须传
	    paramType：参数放在哪个地方
	        · header --> 请求参数的获取：@RequestHeader
	        · query --> 请求参数的获取：@RequestParam
	        · path（用于restful接口）--> 请求参数的获取：@PathVariable
	        · body（请求体）-->  @RequestBody User user
	        · form（不常用）	   
	    dataType：参数类型，默认String，其它值dataType="Integer"	   
	    defaultValue：参数的默认值
	    
示列：
@ApiOperation(value="用户登录",notes="手机号、密码都是必输项，年龄随边填，但必须是数字")
@ApiImplicitParams({
@ApiImplicitParam(name="mobile",value="手机号",required=true,paramType="form"),
@ApiImplicitParam(name="password",value="密码",required=true,paramType="form"),
@ApiImplicitParam(name="age",value="年龄",required=true,paramType="form",dataType="Integer")
})
@PostMapping("/login")
public JsonResult login(@RequestParam String mobile, @RequestParam String password,
@RequestParam Integer age){
...
    return JsonResult.ok(map);
}
```

#### 3.3 `@ApiResponses`、`@ApiResponse`：方法返回值的说明

```java
@ApiResponses：方法返回对象的说明
	@ApiResponse：每个参数的说明
	    code：数字，例如400
	    message：信息，例如"请求参数没填好"
	    response：抛出异常的类
	    
示例：
@ApiOperation("获取用户信息")
@ApiImplicitParams({
	@ApiImplicitParam(paramType = "query", name = "userId", dataType = "String", required = true, value = "用户Id")
}) 
@ApiResponses({
	@ApiResponse(code = 400, message = "请求参数没填好"),
	@ApiResponse(code = 404, message = "请求路径没有或页面跳转路径不对")
}) 
@ResponseBody
@RequestMapping("/list")
public JsonResult list(@RequestParam String userId) {
	...
	return JsonResult.ok().put("page", pageUtil);
}
```

#### 3.4 `@ApiModel`、`@ApiModelProperty`：用于响应类上，表示一个返回响应数据的信息

```java
@ApiModel：用于响应类上，表示一个返回响应数据的信息
			（这种一般用在post创建的时候，使用@RequestBody这样的场景，
			请求参数无法使用 @ApiImplicitParam 注解进行描述的时候）
	@ApiModelProperty：用在属性上，描述响应类的属性

示例:
@ApiModel(description= "返回响应数据")
public class RestMessage implements Serializable{

	@ApiModelProperty(value = "是否成功")
	private boolean success=true;
	@ApiModelProperty(value = "返回对象")
	private Object data;
	@ApiModelProperty(value = "错误编号")
	private Integer errCode;
	@ApiModelProperty(value = "错误信息")
	private String message;
		
	/* getter/setter */
}
```



