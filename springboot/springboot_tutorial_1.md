# 对象关系映射

在这个教程中，我们将使用 [MyBatis](https://mybatis.org/mybatis-3/zh/index.html) 完成Java对象到关系数据库记录的映射。MyBatis是一个支持自定义SQL、存储过程和高级映射的持久层框架，它使用 [XML](https://developer.mozilla.org/zh-CN/docs/Web/XML/XML_introduction) 或者 [注解](https://www.runoob.com/w3cnote/java-annotation.html) 配置对象与关系之间的映射，免除了几乎所有设置参数和获取结果的 [JDBC](https://www.oracle.com/cn/database/technologies/appdev/jdbc.html) 代码。

## 引入依赖

在`pom.xml`中添加MyBatis的依赖：

```
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.1.3</version>
</dependency>
```

因为这里使用MySQL数据库，所以还需添加数据库驱动的依赖

```
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <scope>runtime</scope>
</dependency>
```

## 配置数据库连接

在`application.yml`中配置数据库连接：

```
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/bookkeeping?serverTimezone=GMT%2B8&useUnicode=true&characterEncoding=utf-8
    username: ${MYSQL_USER}
    password: ${MYSQL_PASSWORD}
    driver-class-name: com.mysql.cj.jdbc.Driver
```

## 定义数据表和对象

在MySQL中定义数据表：

```
create table card
(
   c_id                 int not null auto_increment,
   u_id                 int not null,
   c_name               varchar(20) not null,
   primary key (c_id)
);
```

在Spring中定义类：

```
package com.comp2024b.tocountornot.bean;

import lombok.Data;
import org.springframework.stereotype.Component;

@Data
@Component
public class Card {
    private int id;
    private int user;
    private String name;
}
```

这里我们使用`@Component`注解把Card注册为需要Spring管理的通用组件。我们还使用了 [lombok](https://projectlombok.org/) 的`@Data`注解以生成Getters和Setters方法。

## 配置映射

CardMapper.java

```
package com.comp2024b.tocountornot.dao;

import com.comp2024b.tocountornot.bean.Card;
import org.apache.ibatis.annotations.*;
import org.springframework.stereotype.Repository;

import java.util.List;

@Mapper
@Repository
public interface CardMapper {
    @Insert("insert into card (c_id,u_id,c_name) values (#{id},#{user},#{name})")
    @Options(useGeneratedKeys = true, keyColumn = "c_id", keyProperty = "id")
    void insertCard(Card card);

    @Delete("delete from card where c_id=#{id}")
    void deleteCard(@Param("id") int id);

    @Update("update card set c_name=#{name} where c_id=#{id}")
    void updateCard(Card card);

    @Select("select c_id as id,c_name as name from card where u_id=#{uid}")
    List<Card> getAllCard(@Param("uid") int uid);

    @Select("select c_id as id,c_name as name from card where c_id=#{id} and u_id=#{uid}")
    Card getCardById(@Param("id") int id, @Param("uid") int uid);
}
```

这里我们使用spring的`@Repository`注解把CardMapper注册为需要Spring管理的数据访问层组件。我们还使用了`@Mapper`注解把CardMapper标记为MyBatis的映射接口类。

对于不同的SQL语句，我们需要使用与之对应的注解来完成语句映射，如`@Insert`，`@Delete`，`@Update`和`@Select`。SQL语句中不确定的参数需要使用类似`#{param}`的形式标识。如果参数是一个对象，参数名需要和对象的属性名保持一致。如果是单独的参数，需要使用`@Param`注解指定相应的参数。

需要根据SQL语句执行结果的类型指定返回值的类型，如果要返回一个对象，结果中的数据列名需要与对象的属性名保持一致。如果返回多条记录，需要指定返回类型为对象的列表。

我们还可以使用`@Optional`注解和`useGeneratedKeys`来获取自增的主键。其中`keyColumn`是数据表中对应的列，`keyProperty`是对象中的对应的属性。

因为这里的SQL语句比较简单，所以我们使用注解完成映射。对于一些复杂的SQL语句，应考虑使用XML文件完成映射：

CardMapper.xml

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.comp2024b.tocountornot.dao.CardMapper">
  <select id="getCardById" resultType="Card">
    select c_id as id,c_name as name from card where c_id=#{id} and u_id=#{uid}
  </select>
</mapper>
```

有关XML映射，可以参考 [XML映射器](https://mybatis.org/mybatis-3/zh/sqlmap-xml.html)

## 执行语句

每一个基于MyBatis的应用都以一个`SqlSessionFactory`的实例为核心。`SqlSessionFactory`的实例可以由`SqlSessionFactoryBuilder`根据配置文件构建得到，在这里`application.yml`提供了需要的数据源配置信息。我们可以从`SqlSessionFactory`获取`SqlSession`的实例，然后通过`SqlSession`实例执行已映射的SQL语句：

```
try (SqlSession session = sqlSessionFactory.openSession()) {
    CardMapper mapper = session.getMapper(CardMapper.class);
    Card Card = mapper.getCardById(1, 1);
}
```

在Spring中，我们可以通过构造器把mapper注入到service中并进行调用：

CardService.java

```
package com.comp2024b.tocountornot.service;

import com.comp2024b.tocountornot.dao.CardMapper;
import com.comp2024b.tocountornot.exception.NotFoundException;
import org.springframework.stereotype.Service;
import com.comp2024b.tocountornot.bean.Card;

@Service
public class CardService {

    private final CardMapper cardMapper;

    public CardService(CardMapper cardMapper) {
        this.cardMapper = cardMapper;
    }

    public Card getCardById(int id, int uid) {
        Card card = cardMapper.getCardById(id, uid);
        if (card != null) {
            return card;
        } else {
            throw new NotFoundException("card not found");
        }
    }
}
```

这里我们使用`@Service`注解把CardService注册为需要Spring管理的服务层组件。

- [mybatis – MyBatis 3 入门](https://mybatis.org/mybatis-3/zh/getting-started.html)