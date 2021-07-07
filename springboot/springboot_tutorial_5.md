# 身份认证

在这个教程中，我们将使用拦截器和 [JWT](https://jwt.io/) 实现用户身份认证。JWT(Json Web Token)是一个开放的行业标准 [RFC7519](https://datatracker.ietf.org/doc/html/rfc7519) 方法，用于在双方之间安全地传递声明。

## JWT

JWT实际上是由两个`.`分成三个部分的字符串：

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.
eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.
SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

这三个部分分别是头部、负载和签名。

头部用于声明了签名算法和类型：

```
{
  "alg": "HS256",
  "typ": "JWT"
}
```

负载用于携带信息。因为负载中的信息仅使用base64编码，所以不能用于存放敏感信息：

```
{
  "sub": "1234567890",
  "name": "John Doe",
  "iat": 1516239022
}
```

签名用于存放对头部和负载使用密钥和签名算法生成的签名，用于防止对内容的篡改：

```
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  your-256-bit-secret
)
```

## 引入依赖

在`pom.xml`中添加`java-jwt`的依赖：

```
<dependency>
    <groupId>com.auth0</groupId>
    <artifactId>java-jwt</artifactId>
    <version>3.4.0</version>
</dependency>
```

## 编写工具类

下面我们编写一个工具类来封装token的创建，解码和验证操作：

Token.java

```
package com.comp2024b.tocountornot.util;

import com.auth0.jwt.JWT;
import com.auth0.jwt.JWTVerifier;
import com.auth0.jwt.algorithms.Algorithm;
import com.auth0.jwt.exceptions.JWTDecodeException;
import com.auth0.jwt.exceptions.JWTVerificationException;
import com.auth0.jwt.exceptions.TokenExpiredException;
import com.comp2024b.tocountornot.exception.BadRequestException;

import java.util.Calendar;
public class Token {
    private static final String secret = System.getenv("SECRET");

    public static String create(String payload) {
        Calendar calendar = Calendar.getInstance();
        calendar.add(Calendar.DATE, 1);
        return JWT.create()
                .withAudience(payload)
                .withExpiresAt(calendar.getTime())
                .sign(Algorithm.HMAC256(secret));
    }

    public static String decode(String token) {
        String payload;
        try {
            payload = JWT.decode(token).getAudience().get(0);
        } catch (JWTDecodeException jde) {
            throw new BadRequestException("invalid token");
        }
        return payload;
    }

    public static void verify(String token) {
        JWTVerifier jwtVerifier = JWT.require(Algorithm.HMAC256(secret)).build();
        try {
            jwtVerifier.verify(token);
        } catch (TokenExpiredException te) {
            throw new BadRequestException("expired token");
        } catch (JWTVerificationException jve) {
            throw new BadRequestException("invalid token");
        }
    }
}
```

在`create()`方法中，我们使用Calendar类得到次日的时间作为JWT的失效时间。这里的签名算法`HMAC256`需要256位的密钥，建议使用长度为32的随机字符串并妥善保存。

在`decode()`方法中，我们从JWT中解码得到负载信息。

在`verify()`方法中，我们验证JWT的签名以确定其是否已失效或者无效。

## 自定义注解

定义注解`@NoTokenRequired`将注册、登录接口标记为无需身份认证：

NoTokenRequired.java

```
package com.comp2024b.tocountornot.annotation;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface NoTokenRequired {
    boolean required() default true;
}
```

定义注解`@TokenRequired`将其他接口标记为需要身份认证：

TokenRequired.java

```
package com.comp2024b.tocountornot.annotation;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface TokenRequired {
    boolean required() default true;
}
```

其中`@Target`注解指定自定义注解作用于方法，`@Retention`注解指定自定义注解保留到运行时。

## 签发

如果用户在登录中成功通过验证，我们便可向其签发token。

UserService.java

```
public String login(User user) {
    if (ExistUser(user.getName())) {
        User u = getUserByName(user.getName());
        String salt = u.getSalt();
        String hash = Digest.hash(user.getPassword(),salt);
        if (hash.equals(u.getPassword())) {
            return Token.create(String.valueOf(u.getId()));
        } else {
            throw new ForbiddenException("wrong password");
        }
    } else {
        throw new NotFoundException("user not exist");
    }
}
```

在UserService中，我们在确定用户名存在以及密码正确后使用用户id作为负载创建token。

UserController.java

```
@NoTokenRequired
@PostMapping("user/login")
public Result login(@Valid @RequestBody User user, HttpServletResponse response) {
    String token = userService.login(user);
    response.addHeader("Set-Cookie", token);
    return ResultFactory.getSuccessResult();
}
```

在UserController中，我们把创建的token放到响应的Header中返回。

## 验证

要验证用户的token，我们需要定义一个身份认证拦截器，在`preHandle()`方法中获取接口的注解并做出相应的处理。

```
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object object) {
    if (!(object instanceof HandlerMethod)) {
        return true;
    }

    HandlerMethod handlerMethod = (HandlerMethod) object;
    Method method = handlerMethod.getMethod();

    if (method.isAnnotationPresent(NoTokenRequired.class)) {
        NoTokenRequired noTokenRequired = method.getAnnotation(NoTokenRequired.class);
        if (noTokenRequired.required()) {
            return true;
        }
    }

    if (method.isAnnotationPresent(TokenRequired.class)) {
        TokenRequired tokenRequired = method.getAnnotation(TokenRequired.class);
        if (tokenRequired.required()) {
            String token = request.getHeader("Cookie");
            if (token == null) {
                throw new BadRequestException("no token found");
            }
            Token.verify(token);
            int uid = Integer.parseInt(Token.decode(token));
            request.setAttribute("uid", uid);
            return true;
        }
    }

    throw new NotFoundException("method not found");
}
```

对于非方法调用和使用`NoTokenRequired`注解的方法调用，我们直接通过。

对于使用`TokenRequired`注解的方法，我们检查请求头是否携带token并验证token是否有效。

- [JSON Web Token Introduction - jwt.io](https://jwt.io/introduction/)