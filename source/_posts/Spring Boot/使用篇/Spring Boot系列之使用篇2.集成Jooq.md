---
title: Spring Boot系列之使用篇:2.集成Jooq
date: 2019-03-23 23:25:00
author: 康源晟
top: false
cover: true
toc: false
mathjax: false
summary: 集成Jooq
categories: Spring Boot
tags:
  - Spring Boot
  - Jooq
---

### 1.Jooq介绍：

jOOQ是一个基于Java编写SQL的工具包，具有：简单、轻量、函数式编程写SQL等独特优势，非常适合敏捷快速迭代开发。



### 2.集成Jooq

#### 2.1 修改pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.3.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>spring_boot_demo</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <!-- 引入Web模块 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!-- 引入Mysql模块 -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
        </dependency>
        <!-- 引入spring-boot-jooq模块 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jooq</artifactId>
        </dependency>
        <!-- 引入lombok模块 -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
            <plugin>
                <groupId>org.jooq</groupId>
                <artifactId>jooq-codegen-maven</artifactId>
                <version>${jooq.version}</version>
                <executions>
                    <execution>
                        <goals>
                            <goal>generate</goal>
                        </goals>
                    </execution>
                </executions>
                <dependencies>
                    <dependency>
                        <groupId>mysql</groupId>
                        <artifactId>mysql-connector-java</artifactId>
                        <version>${mysql.version}</version>
                    </dependency>
                </dependencies>
                <configuration>
                    <configurationFile>src/main/resources/jooq/JooqConfig.xml</configurationFile>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>

```



#### 2.2 添加JooqConfig.xml配置文件

```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<configuration xmlns="http://www.jooq.org/xsd/jooq-codegen-3.11.0.xsd">
    <!-- Configure the database connection here -->
    <jdbc>
        <driver>com.mysql.jdbc.Driver</driver>
        <url>jdbc:mysql://localhost:3306/spring_boot_db</url>
        <user>root</user>
        <password></password>
    </jdbc>
    <generator>
        <!-- The default code generator. You can override this one, to generate your own code style.
              Supported generators:
                - org.jooq.codegen.JavaGenerator
                - org.jooq.codegen.ScalaGenerator
                Defaults to org.jooq.codegen.JavaGenerator -->
        <name>org.jooq.codegen.JavaGenerator</name>

        <database>
            <!-- The database type. The format here is:
                    org.util.[database].[database]Database -->
            <name>org.jooq.meta.mysql.MySQLDatabase</name>

            <!-- The database schema (or in the absence of schema support, in your RDBMS this can be the owner, user, database name) to be generated -->
            <inputSchema>spring_boot_db</inputSchema>

            <!-- All elements that are generated from your schema (A Java regular expression. Use the pipe to separate several expressions) Watch out for case-sensitivity. Depending on your database, this might be important! -->
            <includes>.*</includes>

            <!-- All elements that are excluded from your schema (A Java regular expression. Use the pipe to separate several expressions). Excludes match before includes, i.e. excludes have a higher priority -->
            <excludes></excludes>
        </database>

        <target>
            <!-- The destination package of your generated classes (within the destination directory) -->
            <packageName>com.example.demo.db.jooq</packageName>

            <!-- The destination directory of your generated classes. Using Maven directory layout here -->
            <directory>src/main/java</directory>
        </target>
    </generator>
</configuration>
```



#### 2.3 在application.properties中配置数据源

```properties
#datasource
#驱动名
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
#url
spring.datasource.url=jdbc:mysql://localhost:3306/spring_boot_db?characterEncoding=utf-8
#用户名
spring.datasource.username=root
#密码
spring.datasource.password=
```

 

#### 2.4 生成jooq文件

先clean然后在点击compile来生成jooq文件。

![](http://kyshblogs.oss-cn-beijing.aliyuncs.com/Spring-Boot/Spring-Boot-0008.png)

### 3.编写测试用例

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class JooqTests   {

    @Autowired
    private DSLContext dsl;

    private CoreUserTbl coreUserTbl = CoreUserTbl.CORE_USER_TBL;

    @Test
    public void addUserTest() {
        UserAddBo userAddBo = new UserAddBo();
        userAddBo.setUsername("admin");
        userAddBo.setPassword("123456");

       int state =  dsl.insertInto(coreUserTbl).
                columns(coreUserTbl.USERNAME_, coreUserTbl.PASSWORD_).
                values(userAddBo.getUsername(), userAddBo.getPassword())
                .execute();

        Assert.assertEquals(state, 1);
    }

    @Test
    public void updateUserTest() {
        CoreUserTblRecord coreUserTblRecord = new CoreUserTblRecord();
        coreUserTblRecord.setId_(1L);
        coreUserTblRecord.setUsername_("admin");
        coreUserTblRecord.setPassword_("00000");

        int state =   dsl.update(coreUserTbl).set(coreUserTblRecord).execute();

        Assert.assertEquals(state, 1);
    }

    @Test
    public void deleteUserTest() {

        int state = dsl.delete(coreUserTbl).where(coreUserTbl.ID_.eq(1L)).execute();

        Assert.assertEquals(state, 1);
    }
}

```





