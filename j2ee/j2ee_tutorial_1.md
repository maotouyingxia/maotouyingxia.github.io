# 基础教程

## 安装IDEA

在这个教程中，我们将使用 [IntelliJ IDEA](https://www.jetbrains.com/idea/) 创建一个 [J2EE](https://www.oracle.com/java/technologies/appmodel.html) 项目。IDEA是广受欢迎的Java开发集成环境。如果你是学生，可以通过 [免费教育许可证](https://www.jetbrains.com/zh-cn/community/education/#students) 获取License并下载使用IntelliJ IDEA Ultimate。

## 新建项目

打开IDEA，点击右上角的`New Project`新建项目。

在左侧的目录中选择`Java Enterprise`，然后完成项目配置：

```
Name: demo

Location: E:\IDEA\demo

Project template: Web application

Application server: Tomcat 9.0.44

Build system: Maven

Language: Java

Test framework: JUnit

Group: com.example

Artifact: demo

Project SDK: 1.8
```

- [Tomcat](https://tomcat.apache.org/) 是一个Servlet的容器，它可以动态地创建，调用和销毁Servlet实例
- [Maven](http://maven.apache.org/) 是一个依赖管理工具，用于构建项目
- [JUnit](https://junit.org/junit5/) 是一个单元测试框架，用于编写单元测试

点击`Next`进入项目依赖页面。因为`Servlet`已添加到依赖中。所以点击`Finish`创建项目即可。

## 项目文件

IDEA为项目生成了如下文件：

```
E:.
│  demo.iml
│  pom.xml
│
└─src
    ├─main
    │  ├─java
    │  │  └─com
    │  │      └─example
    │  │          └─demo
    │  │                  HelloServlet.java
    │  │
    │  ├─resources
    │  └─webapp
    │      │  index.jsp
    │      │
    │      └─WEB-INF
    │              web.xml
    │
    └─test
        ├─java
        └─resources
```

`demo.iml`是IDEA的配置文件。

`WEB-INF/web.xml`是项目的配置文件。

`pom.xml`是Maven用于构建项目的文件，可用于管理依赖：

```
<dependencies>
    <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>4.0.1</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-api</artifactId>
        <version>${junit.version}</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-engine</artifactId>
        <version>${junit.version}</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

## Servlet

Web服务主要包含接收请求、处理请求和响应请求三个步骤。对于不同的Web服务，接收请求和响应请求都是相似的，所以这部分可由Web服务器完成，比如Tomcat。而对于处理请求中不同的业务逻辑，则需要通过Servlet编写。

在Web服务中，Tomcat会解析收到的HTTP请求并将其封装成Request对象，接着读取web.xml中Servlet的配置信息，然后通过反射创建相应的Servlet实例，并传入参数Request对象和空的Response对象进行调用。Servlet则根据Request对Response做出相应的处理。最后Tomcat把处理后的Response组装成HTTP响应发给客户端。

在Servlet中，我们主要需要实现init(), service(), destroy()三个方法。其中init()和destroy()分别在Servlet创建和销毁时被调用。service()则会在每次有新的请求时被调用，所以我们需要在里面实现业务逻辑。

又因为HTTP请求具有多种方式，所以HttpServlet在service()方法中实现了对请求方法的判断但并未给出有效的实现（因为具体的业务逻辑是未知的）。所以我们只需要继承HttpServlet并重写doGet()和doPost()方法以实现具体的业务逻辑。

下面我们看看模板生成的Servlet文件：

HelloServlet.java

```
package com.example.demo;

import java.io.*;
import javax.servlet.http.*;
import javax.servlet.annotation.*;

@WebServlet(name = "helloServlet", value = "/hello-servlet")
public class HelloServlet extends HttpServlet {
    private String message;

    public void init() {
        message = "Hello World!";
    }

    public void doGet(HttpServletRequest request, HttpServletResponse response) throws IOException {
        response.setContentType("text/html");

        // Hello
        PrintWriter out = response.getWriter();
        out.println("<html><body>");
        out.println("<h1>" + message + "</h1>");
        out.println("</body></html>");
    }

    public void destroy() {
    }
}
```

在这里，我们使用`@WebServlet`注解代替了web.xml中的配置，并指定`helloServlet`对应的请求为`/hello-servlet`。

在init()方法中，我们初始化了要发送的信息。

在doGet()方法中，我们指定了response的类型，并向其写入作为响应的HTML文件。

## JSP

在上面的部分中，你可能注意到Servlet生成HTML文件的方式相当繁琐。我们可以使用JSP来解决这个问题。JSP看起来就像是嵌入Java代码的HTML文件，但它并不会被作为响应发给客户端。实际上，JSP会在服务器被编译为Servlet并通过内嵌的Java代码获取数据并拼接到HTML文件中，进而生成静态的HTML文件作为响应发送到客户端。

下面我们看看模板生成的JSP文件：

index.jsp

```
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <title>JSP - Hello World</title>
</head>
<body>
<h1><%= "Hello World!" %>
</h1>
<br/>
<a href="hello-servlet">Hello Servlet</a>
</body>
</html>
```

在这里，我们向HTML文件中嵌入了Java字符串`Hello World!`。

## 运行项目

点击IDEA右上方的绿色运行运行按钮。Tomcat启动后会打开浏览器。你可以在浏览器看到`Hello World!`和一个超链接`Hello Servlet`，这是index.jsp生成的响应。点击超链接`Hello Servlet`，你可以看到`Hello World!`，这是helloServlet生成的响应。

- [servlet的本质是什么，它是如何工作的？ - 知乎](https://www.zhihu.com/question/21416727/answer/690289895)
- [怎样学习JSP？ - 知乎](https://www.zhihu.com/question/23984162/answer/689106407)