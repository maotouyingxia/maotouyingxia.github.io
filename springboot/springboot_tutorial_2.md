# 全局异常处理

在这个教程中，我们将统一处理异常并返回相应的HTTP状态码和提示信息。

## 定义状态码

Code.java

```
package com.comp2024b.tocountornot.util;

public enum Code {
    BAD_REQUEST(400),
    UNAUTHORIZED(401),
    FORBIDDEN(403),
    NOT_FOUND(404),
    CONFLICT(409),
    INTERNAL_SERVER_ERROR(500);

    private final int code;

    Code(int code) {
        this.code = code;
    }

    public int getCode() {
        return code;
    }
}
```

这里我们使用枚举类定义了几种常见的HTTP状态码。

## 定义返回结果

Result.java

```
package com.comp2024b.tocountornot.util;

import lombok.Data;

@Data
public class Result {
    private int code;
    private String message;
    private Object data;

    public Result setCode(Code code) {
        this.code = code.getCode();
        return this;
    }

    public Result setMessage(String message) {
        this.message = message;
        return this;
    }

    public Result setData(Object data) {
        this.data = data;
        return this;
    }
}
```

这里我们定义了包含状态码，提示信息和数据的结果类。

## 定义结果工厂

ResultFactory.java

```
package com.comp2024b.tocountornot.util;

public static Result getNotFoundResult(String message) {
    return new Result().setCode(Code.NOT_FOUND).setMessage(message);
}
```

这里我们以`404 Not Found`为例，定义了生成相应结果的静态方法。

## 定义异常

NotFoundException.java

```
package com.comp2024b.tocountornot.exception;

public class NotFoundException extends RuntimeException {
    public NotFoundException(String message) { super(message); }
}
```

这里我们继承`RuntimeException`定义了对应于`404 Not Found`的异常。

## 异常处理

GlobalExceptionHandler.java

```
package com.comp2024b.tocountornot.exception;

import com.comp2024b.tocountornot.util.Result;
import com.comp2024b.tocountornot.util.ResultFactory;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(value = NotFoundException.class)
    public Result NotFoundExceptionHandler(NotFoundException nfe) {
        return ResultFactory.getNotFoundResult(nfe.getMessage());
    }
```

这里我们使用`@RestControllerAdvice`注解让`GlobalExceptionHandler`能够捕获所有controller抛出的异常。以`404 Not Found`为例，我们定义了对应的异常处理方法，并使用`@ExceptionHandler`注解指定了要处理的异常类型。

在用户请求的资源不存在时，我们可以抛出异常并给出提示信息：

```
Card card = cardMapper.getCardById(id, uid);
if (card != null) {
    return card;
} else {
    throw new NotFoundException("card not found");
}
```

全局异常处理会捕获这个异常并返回处理后的结果：

```
{
    "code": 404,
    "message": "card not found",
    "data": null
}
```

- [Spring Boot 异常处理的几种方式](https://snailclimb.gitee.io/springboot-guide/#/./docs/advanced/springboot-handle-exception)