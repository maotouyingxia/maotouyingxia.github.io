# 使用Spring开发REST服务

REST很快成为了网上开发web服务事实上的标准，因为它们易于构建和使用。

对于REST是如何适合于微服务的世界还有很大的讨论空间，但在这个教程里，我们只关心如何建立RESTful服务。

为什么是REST？REST遵守web的戒律，包括它的结构，好处和其他的一切。这并不令人惊讶，因为它的提出者Roy Fielding曾参与制定了一系列约束web如何运作的规则。

好处都有哪些？web和它的核心协议HTTP提供了丰富的特性：
- 合适的请求方法（`GET`，`POST`，`PUT`，`DELETE`，...）
- 缓存
- 重定向和转发
- 安全（加密与认证）

这些都是开发弹性服务的关键性因素。但这还不是全部。web建立于大量的简单规则之上，这使得它易于演化，并且不会陷于“标准战争”的困境。

开发者们能够依靠实现了这些丰富标准的第三方工具迅速地让客户端服务器技术变得触手可及。

建立在HTTP之上，REST APIs能够开发出：
- 与后端兼容的APIs
- 可扩展的服务
- 安全的服务
- 一系列无状态、有状态的服务

需要意识到的是，REST虽然无处不在，但它并不是一个标准，而是有助于开发网络规模系统的一个方式，一种风格，一整套项目结构的约束条件。在这个教程里，我们将使用Spring工具包开发RESTful服务并利用REST无堆栈的特性。

## 入门

在这个教程中，我们会用到 [Spring Boot](https://spring.io/projects/spring-boot) 。在 [Spring Initializer](https://start.spring.io/) 中为项目添加如下依赖：
- Web
- JPA
- H2

将名字改为"Payroll"然后点击“Generate Project”。下载生成的zip文件并解压。这是一个简单的基于Maven的包含`pom.xml`构建文件的项目（注意，你可以使用Gradle，但这个教程里的例子是基于Maven的）。

Spring Boot可以在任何IDE下工作。你可以使用Eclipse, IntelliJ IDEA, Netbeans...

## 到目前为止

让我们从能够做的最简单的东西开始。事实上，为了让它尽可能的简单，我们甚至可以暂时不考虑REST的概念。（在后面，我们会加入REST来理解这个区别）。

总的来说，我们将建立一个简单的公司员工工资表服务。我们会把员工对象存储到H2内存数据库中，并使用JPA访问它们。接着对它进行封装使其能够通过网络访问（即Spring MVC层）。

下面的代码定义了我们系统中的一个员工。

nonrest/src/main/java/payroll/Employee.java

```
package payroll;

import java.util.Objects;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

@Entity
class Employee {

  private @Id @GeneratedValue Long id;
  private String name;
  private String role;

  Employee() {}

  Employee(String name, String role) {

    this.name = name;
    this.role = role;
  }

  public Long getId() {
    return this.id;
  }

  public String getName() {
    return this.name;
  }

  public String getRole() {
    return this.role;
  }

  public void setId(Long id) {
    this.id = id;
  }

  public void setName(String name) {
    this.name = name;
  }

  public void setRole(String role) {
    this.role = role;
  }

  @Override
  public boolean equals(Object o) {

    if (this == o)
      return true;
    if (!(o instanceof Employee))
      return false;
    Employee employee = (Employee) o;
    return Objects.equals(this.id, employee.id) && Objects.equals(this.name, employee.name)
        && Objects.equals(this.role, employee.role);
  }

  @Override
  public int hashCode() {
    return Objects.hash(this.id, this.name, this.role);
  }

  @Override
  public String toString() {
    return "Employee{" + "id=" + this.id + ", name='" + this.name + '\'' + ", role='" + this.role + '\'' + '}';
  }
}
```

虽然很小，这个Java类包含了很多东西：
- `@Entity`是让对象能够存放在JPA数据库的注解
- `id`，`name`和`role`是Employee域对象的属性。`id`的注解表明它是主键且由JPA提供者自动填充。
- 创建了一个自定义的构造新对象的构造函数，但并没有id

在定义了域对象之后，我们现在可以使用 [Spring Data JPA](https://spring.io/guides/gs/accessing-data-jpa/) 处理乏味的数据库交互。

Spring Data JPA仓库是可对后端数据库做出创建，读取，更新和删除记录操作的方法接口。一些仓库在某些合适的地方还支持数据分页和分类。Spring Data基于接口中方法命名的约定综合实现。

```
除了JPA还有许多的仓库实现。你可以使用Spring Data MongoDB，Spring Data GemFire，Spring Data Cassandra...在这个教程里我们使用JPA。
```

Spring让访问数据变得容易。仅仅通过声明以下的`EmployeeRepository`接口我们自动地能够：
- 创建新的员工
- 更新存在的员工
- 删除员工
- 查找员工（单个，全部，或者使用简单或是复杂的参数）

nonrest/src/main/java/payroll/EmployeeRepository.java

```
package payroll;

import org.springframework.data.jpa.repository.JpaRepository;

interface EmployeeRepository extends JpaRepository<Employee, Long> {

}
```

为了得到这些功能，我们需要做的只是声明一个继承于Spring Data JPA的`JpaRespository`的接口并指定域类型为`Employee`，id类型为`Long`。

Spring Data的仓库解决方案让回避具体的数据存储成为可能并解决了使用特定领域术语的问题。

信不信由你，这些已经足够让应用运行起来了！一个Spring Boot应用至少包括一个`public static void main`的入口和`@SpringBootApplication`注解。这些能让Spring Boot在尽可能多的地方起作用。

`nonrest/src/main/java/payroll/PayrollApplication.java`

```
package payroll;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class PayrollApplication {

  public static void main(String... args) {
    SpringApplication.run(PayrollApplication.class, args);
  }
}
```

`@SpringBootApplication`是一个引入构建搜索，自动配置和参数支持的元注解。在这个教程里我们不会深入Spring Boot的细节，但本质上来说，它会启动一个servlet容器并为我们的服务提供支持。

虽说如此，但一个没有数据的应用程序并不是很有趣，所以让我们预先载入数据。下面的类会自动被Spring加载：

`nonrest/src/main/java/payroll/LoadDatabase.java`

```
package payroll;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.CommandLineRunner;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
class LoadDatabase {

  private static final Logger log = LoggerFactory.getLogger(LoadDatabase.class);

  @Bean
  CommandLineRunner initDatabase(EmployeeRepository repository) {

    return args -> {
      log.info("Preloading " + repository.save(new Employee("Bilbo Baggins", "burglar")));
      log.info("Preloading " + repository.save(new Employee("Frodo Baggins", "thief")));
    };
  }
}
```

当它被载入时会发生什么？
- 当这个应用程序上下文载入时Spring Boot会运行所有`CommandLineRunner`组件
- 这个启动器会请求一个你刚刚创建的`EmployeeRepository`的副本
- 使用它，它将会创建两个实体并保存它们。

右键点击并运行`PayRollApplication`，你将会得到这些：

一部分展示数据载入的控制台输出

```
...
2018-08-09 11:36:26.169  INFO 74611 --- [main] payroll.LoadDatabase : Preloading Employee(id=1, name=Bilbo Baggins, role=burglar)
2018-08-09 11:36:26.174  INFO 74611 --- [main] payroll.LoadDatabase : Preloading Employee(id=2, name=Frodo Baggins, role=thief)
...
```

## HTTP作为平台

to be continued...

- [Building REST services with Spring](https://spring.io/guides/tutorials/rest/)