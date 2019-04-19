---
title: Spring Boot系列之使用篇:8.Spring AOP切面统一打印请求日志
date: 2019-03-24 09:55:00
author: 康源晟
top: false
cover: true
toc: false
mathjax: false
summary: 使用Spring AOP切面统一打印请求日志
categories: Spring Boot
tags:
  - Spring Boot
  - log4j2
  - AOP
---

### 1.Log4j2 介绍

Spring Boot 中默认使用 Logback 作为日志框架，接下来我们将学习如何在 Spring Boot 中集成与配置 Log4j2。在配置之前，我们需要知道的是 Log4j2 是 Log4j 的升级版，它在 Log4j 的基础上做了诸多改进：

- 异步日志；
- 支持 Java8 lambda 风格的懒加载日志；
- 过滤器；
- 插件；
- 并发性改进；
- 支持： SLF4J, Commons Logging, Log4j-1.x 以及 java.util.logging;
- 配置热加载;
- 自定义日志级别；



### 2.配置Log4j2 

#### 2.1 添加Maven依赖

Spring Boot 默认使用的是 logback, 想要使用 Log4j2, 我们需要首先排除掉默认的日志框架，然后添加 log4j2 依赖。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-logging</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
```



#### 2.2 在application.properties添加配置

```properties
#Log4j2配置
status = error
name = Log4j2Sample
appenders = console

appender.console.type = Console
appender.console.name = STDOUT
appender.console.layout.type = PatternLayout
appender.console.layout.pattern = %d{yyyy-MM-dd HH:mm:ss} - %msg%n

rootLogger.level = warn
rootLogger.appenderRefs = stdout
rootLogger.appenderRef.stdout.ref = STDOUT
```



#### 2.3 测试

```java
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest()
public class ApplicationTests2 {

    private static final Logger logger = LogManager.getLogger(ApplicationTests2.class);


    @Test
    public void getHello() throws Exception {
        logger.debug("debug 级别日志 ...");
        logger.info("info 级别日志 ...");
        logger.warn("warn 级别日志 ...");
        logger.error("error 级别日志 ...");
        logger.fatal("fatal 级别日志 ...");
    }
}

控制台:
2019-03-24 10:15:58.434  INFO 7640 --- [           main] c.e.d.t.ApplicationTests2                : info 级别日志 ...
2019-03-24 10:15:58.434  WARN 7640 --- [           main] c.e.d.t.ApplicationTests2                : warn 级别日志 ...
2019-03-24 10:15:58.434 ERROR 7640 --- [           main] c.e.d.t.ApplicationTests2                : error 级别日志 ...
2019-03-24 10:15:58.434 FATAL 7640 --- [           main] c.e.d.t.ApplicationTests2                : fatal 级别日志 ...
```



### 3.配置 AOP 切面

#### 3.1添加 Maven 依赖

```xml
<!-- 引入AOP模块 -->
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

#### 3.2 创建切面类

```java
/**
 * @Configuration注解声明 AOPConfig 类为一个配置类，将它交给 Spring 管理；
 *
 * @Aspect 声明这是一个切面类；
 */
@Configuration
@Aspect
public class AOPConfig {

    /**
     * @Around 声明了一个表达式，描述的是需要织入的目标，
     * @within 表示凡是类上带有注解类型为 org.springframework.web.bind.annotation.RestController 的，
     * 一旦类中的方法被调用，都会执行 @Around 注解的方法，即是上面代码中 simpleAop() 方法。
     *
     * simpleAop() 表示需要织入的代码，参数为 ProceedingJoinPoint，
     * 方法中 proceedingJoinPoint.getArgs() 表示获取目标方法的参数，转成 json 并打印到控制台。
     *
     * pjp.proceed() 表示调用目标方法，也就是我们需要做切面的业务逻辑方法，并返回目标方法的返回值。
     *
     * 注意：调用目标方法可能会抛出异常，我们这里不捕获，而是抛出给调用者。
     */
    @Around("@within(org.springframework.web.bind.annotation.RestController)")
    public Object simpleAop(final ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        Object[] args = proceedingJoinPoint.getArgs();
        System.out.println("入参: " + JSONObject.toJSONString(Arrays.asList(args)));
        // 调用目标方法
        Object object = proceedingJoinPoint.proceed();
        System.out.println("返参: " + object);
        return object;
    }
}
```



### 4.AOP 切面统一打印请求日志

```java
/**
 * @Aspect：声明该类为一个注解类；
 */
@Aspect
@Component
public class WebLogAspect {

    private final static Logger logger = LoggerFactory.getLogger(WebLogAspect.class);

    /**
     * @Pointcut：定义一个切点，后面跟随一个表达式，
     * 表达式可以定义为某个 package 下的方法，也可以是自定义注解等；
     *
     * 以 controller 包下定义的所有请求为切入点
     */
    @Pointcut("execution(public * com.example.demo.controller..*.*(..))")
    public void webLog() {}

    /**
     * @Before: 在切点之前，织入相关代码；
     *
     * @param joinPoint
     * @throws Throwable
     */
    @Before("webLog()")
    public void doBefore(JoinPoint joinPoint) throws Throwable {
        // 开始打印请求日志
        ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
        HttpServletRequest request = attributes.getRequest();

        // 打印请求相关参数
        logger.info("========================================== Start ==========================================");
        // 打印请求 url
        logger.info("URL            : {}", request.getRequestURL().toString());
        // 打印 Http method
        logger.info("HTTP Method    : {}", request.getMethod());
        // 打印调用 controller 的全路径以及执行方法
        logger.info("Class Method   : {}.{}", joinPoint.getSignature().getDeclaringTypeName(), joinPoint.getSignature().getName());
        // 打印请求的 IP
        logger.info("IP             : {}", request.getRemoteAddr());
        // 打印请求入参
        logger.info("Request Args   : {}", JSONObject.toJSONString(joinPoint.getArgs()));
    }

    /**
     * @After: 在切点之后，织入相关代码;
     *
     * @throws Throwable
     */
    @After("webLog()")
    public void doAfter() throws Throwable {
        logger.info("=========================================== End ===========================================");
        // 每个请求之间空一行
        logger.info("");
    }

    /**
     * @Around: 在切入点前后织入代码，并且可以自由的控制何时执行切点；
     *
     * @param proceedingJoinPoint
     * @return
     * @throws Throwable
     */
    @Around("webLog()")
    public Object doAround(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        long startTime = System.currentTimeMillis();
        Object result = proceedingJoinPoint.proceed();
        // 打印出参
        logger.info("Response Args  : {}", JSONObject.toJSONString(result));
        // 执行耗时
        logger.info("Time-Consuming : {} ms", System.currentTimeMillis() - startTime);
        return result;
    }

    /**
     * @AfterReturning: 在切点返回内容后，织入相关代码，一般用于对返回值做些加工处理的场景；
     *
     * @AfterThrowing: 用来处理当织入的代码抛出异常后的逻辑处理;
     */

}
```

