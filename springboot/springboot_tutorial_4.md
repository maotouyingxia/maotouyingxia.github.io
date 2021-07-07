# 接口调用日志

在Spring中，请求在被controller处理之前需要经过interceptor，即拦截器的处理。因此，我们可以使用拦截器来生成接口调用日志。

## 定义拦截器

通常，我们通过实现`HandlerInterceptor`接口并重写`preHandle()`,`postHandle()`和`afterCompletion()`三个方法来自定义拦截器。

LogInterceptor.java

```
package com.comp2024b.tocountornot.interceptor;

import com.comp2024b.tocountornot.ToCountOrNotApplication;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class LogInterceptor implements HandlerInterceptor {
    private static final Logger logger = LoggerFactory.getLogger(ToCountOrNotApplication.class);

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) {
        long startTime = System.currentTimeMillis();
        request.setAttribute("startTime", startTime);
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) {
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {
        long startTime = (long) request.getAttribute("startTime");
        long endTime = System.currentTimeMillis();
        String invokeInfo = request.getRemoteAddr() + " " +
                request.getMethod() + " " +
                request.getRequestURI() + " " +
                "handled in " + (endTime - startTime) + "ms";
        logger.info(invokeInfo);
    }
}
```

`preHandle()`方法用来决定是否拦截某个请求，因为这里要对所有的接口调用生成日志，所以在请求中设置了处理的起始时间后，我们返回`true`以通过所有请求。

`postHandle()`方法在controller完成处理之后，视图渲染之前被调用，用来对`ModelAndView`进行操作。

`afterCompletion()`方法在请求处理完成之后调用。这里我们获取了请求的IP地址，方法和接口。然后用`endTime`减去`preHandle()`中设置的`startTime`得到处理时长。最后使用`LoggerFactory`获取SpringBoot应用的`logger`并以`info`级别打印了接口调用信息。

## 配置拦截器

要使用拦截器，我们还需要实现`WebMvcConfigurer`接口并重写`addInterceptors()`方法来添加上面定义的拦截器：

InterceptorConfig.java

```
package com.comp2024b.tocountornot.interceptor;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class InterceptorConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(logInterceptor());
    }
    @Bean
    public HandlerInterceptor logInterceptor() { return new LogInterceptor(); }
}
```

可以使用多个不同的拦截器来实现不同的功能，请求会按照拦截器配置中的添加顺序被拦截器依次处理。

最后，日志拦截器会生成类似下面的接口调用日志：

`0:0:0:0:0:0:0:1 GET /cards handled in 97ms`

- [SpringBoot 实现拦截器](https://snailclimb.gitee.io/springboot-guide/#/./docs/basis/springboot-interceptor)