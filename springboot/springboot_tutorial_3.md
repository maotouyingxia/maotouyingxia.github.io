# 参数校验

在这个教程中，我们将使用`spring-boot-starter-validation`完成参数校验。

## 引入依赖

在`pom.xml`中添加`spring-boot-starter-validation`的依赖

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

## 验证请求体

在我们需要验证的请求体前加上`@Valid`注解：

```
public Result insertCard(@Valid @RequestBody Card card) {
    cardService.insertCard(card);
    return ResultFactory.getCreatedResult(card.getId());
}
```

在对应的类定义中给出校验条件：

```
package com.comp2024b.tocountornot.bean;

import lombok.Data;
import org.springframework.stereotype.Component;

import javax.validation.constraints.NotBlank;
import javax.validation.constraints.Size;

@Data
@Component
public class Card {
    private int id;

    @NotBlank(message = "name cannot be blank")
    @Size(max=20, message = "name length cannot exceed 20")
    private String name;
}
```

`@NotBlank`注解用于字符串，表示字符串不能为null，长度不能为0且不能全由空格组成。`@Size`注解用于验证对象的大小，这里使用`max`限制字符串长不能超过20。如果参数不符合要求，会抛出`MethodArgumentNotValidException`异常，可以使用`message`给出异常信息。通常我们会在全局异常处理中捕获这个异常：

```
@ExceptionHandler(value = MethodArgumentNotValidException.class)
public Result MethodArgumentNotValidExceptionHandler(MethodArgumentNotValidException mae) {
    return ResultFactory.getBadRequestResult(mae.getBindingResult().getFieldError().getDefaultMessage());
}
```

并在处理异常后返回相应的结果：

```
{
    "code": 400,
    "message": "name cannot be blank",
    "data": null
}
```

## 验证请求参数

要验证请求参数，除了需要在请求参数前加上`@Valid`注解和校验注解：

`@Valid @PathVariable("month") @Range(min=1,max=12) int month`

我们还需要在对应的controller上加上`@Validated`注解让Spring去校验请求参数。

在请求参数不符合要求时，会抛出`ConstraintViolationException`异常，我们需要进行与验证请求体中类似的异常处理。

除了前面提到的校验注解，常用的校验注解还有：

| constraint | detail |
| ---------- | ------ |
| @Null | 元素必须为null |
| @NotNull | 元素必须不为null |
| @AssertTrue | 元素必须为True |
| @AssertFalse | 元素必须为False |
| @Min(value) | 元素必须是大于等于value的整数 |
| @Max(value) | 元素必须是小于等于value的整数 |
| @DecimalMin(value) | 元素必须是大于等于value的实数 |
| @DecimalMax(value) | 元素必须是小于等于value的实数 |
| @Size(min=,max=) | 元素的大小必须在min和max之间 |
| @Digits(integer=,fraction=) | 元素必须是整数位数不超过integer且小数位数不超过fraction的数字 |
| @Past | 元素必须是过去的日期 |
| @Future | 元素必须是将来的日期 |
| @Pattern(regexp=) | 元素必须是与正则表达式regexp匹配的字符串 |
| @NotBlank | 元素必须是非空字符串且不能全由空格组成 |
| @Email | 元素必须是电子邮箱地址 |
| @Length(min=,max=) | 元素必须是长度在min和max之间的字符串 |
| @NotEmpty | 元素必须是非空字符串 |
| @Range(min=,max=) | 元素的范围必须在min和max之间 |

如果没有你需要的注解，你还可以通过实现`ConstraintValidator`接口并重写`isValid()`方法自定义注解。

- [如何在 Spring/Spring Boot 中优雅地做参数校验？](https://snailclimb.gitee.io/springboot-guide/#/./docs/spring-bean-validation)