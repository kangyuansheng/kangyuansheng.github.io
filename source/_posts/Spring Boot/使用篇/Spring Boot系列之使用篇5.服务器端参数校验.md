---
title: Spring Boot系列之使用篇:5.服务器端参数校验
date: 2019-03-23 23:28:00
author: 康源晟
top: false
cover: true
toc: false
mathjax: false
summary: 本章将详细讲解Spring Boot服务器端参数校验
categories: Spring Boot
tags:
  - Spring Boot
---

### 1.之前的服务端校验：

```java
/**
 * @param menuUpdateBo 菜单修改数据Bo
 * @return
 * @Description:校验修改数据是否正确
 * @author kangyuansheng
 * @date 2018/10/30 14:17
 */
@Override
public boolean checkUpdateMenu(MenuUpdateBo menuUpdateBo) {
    if (menuUpdateBo == null) {
        return false;
    }
    //用户id必须输入
    if (menuUpdateBo.getId() == null || menuUpdateBo.getId() <= 0) {
        return false;
    }
    //名称必须输入
    if (StringUtils.isNotBlank(menuUpdateBo.getName())) {
        return false;
    }
    return true;
}
```



### 2. 新验证框架

Bean Validator 和 Hibernate Validator 就是两套用于验证的框架，二者都遵循 JSR-303 ，可以混着用，但是在某些注解上存在偏差。

Bean Validation 中内置的约束：

| 注解                       | 作用                                                         |
| -------------------------- | ------------------------------------------------------------ |
| @Null                      | 被注解参数必须为空                                           |
| @NotNull                   | 被注解参数不能为空                                           |
| @AssertTrue                | 被注解参数必须为 True                                        |
| @AssertFalse               | 被注解参数必须为 False                                       |
| @Min(value)                | 被注解参数必须是数字，且其值必须大于等于 value               |
| @Max(value)                | 被注解参数必须是数字，且其值必须小于等于 value               |
| @DecimaMin(value)          | 被注解参数必须是数字，且其值必须大于等于 value               |
| @DecimaMax(value)          | 被注解参数必须是数字，且其值必须小于等于 value               |
| @Size(max, min)            | 被注解参数大小必须在指定范围内                               |
| @Past                      | 被注解参数必须是一个过去的日期                               |
| @Future                    | 被注解参数必须是一个将来的日期                               |
| @Pattern(value)            | 被注解参数必须符合指定的正则表达式                           |
| @Digits(integer, fraction) | 被注解参数必须是数字，且其值必须在可接受范围内               |
| @NotBlank                  | 被注解参数的值不为空（不为 null、去除首位空格后长度为 0），不同于 @NotEmpty，@NotBlank 只应用于字符串且在比较时会去除字符串的空格 |

Hibernate Validator 附加的约束：

| 注解      | 作用                                                         |
| --------- | ------------------------------------------------------------ |
| @NotEmpty | 被注解参数的值不为 null 且不为空（字符串长度不为0、集合大小不为0） |
| @Email    | 被注解参数必须是电子邮箱地址                                 |
| @Length   | 被注解的字符串长度必须在指定范围内                           |
| @Range    | 被注解的参数必须在指定范围内                                 |



### 3.测试

##### 3.1 编写测试用例

```java
@RestController
public class UserControllerTests {

    /**
     * 普通参数校验
     * @param name
     * @return
     */
    @GetMapping("/name")
    public String findStudentByName(@NotBlank(message = "名字不能为空")
                                    @Length(min = 2, max = 10, message = "name 长度必须在 {min} - {max} 之间")String name){
        return "success";
    }

    /**
     * 对象校验
     * @param userAddTestDmnObj
     * @return
     */
    @PostMapping("/addUsertest")
    public String addBridge(@Validated @RequestBody UserAddTestDmnObj userAddTestDmnObj) {
        return "success";
    }
}
```

##### 3.2 对象校验校验结果：

![](http://kyshblogs.oss-cn-beijing.aliyuncs.com/Spring-Boot/Spring-Boot-0010.png)