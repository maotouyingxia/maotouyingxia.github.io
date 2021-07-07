# 数据库连接

在这个教程中，我们将使用 [JDBC](https://www.oracle.com/cn/database/technologies/appdev/jdbc.html) 连接到数据库。JDBC是一套用于在Java和数据库之间建立连接的API标准。各个数据库厂商都依据这个标准实现了它们各自的数据库驱动。在这里我们使用 [MySQL](https://www.mysql.com/) 提供的 [Connector/J](https://dev.mysql.com/downloads/connector/j/) 连接到MySQL数据库。

## 安装Connector/J

下面我们通过在Maven中引入依赖的方式安装Connector/J。如果你需要使用其他方式，可以参考 [Connector/J Installation](https://dev.mysql.com/doc/connector-j/8.0/en/connector-j-installing.html) 。在`pom.xml`中添加如下依赖：

```
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.23</version>
</dependency>
```

然后使用`load maven changes`让Maven引入这个依赖。

## 连接到数据库

接下来我们使用 [单例模式](https://www.runoob.com/design-pattern/singleton-pattern.html) 编写一个数据库连接的工具类。

```
package com.comp3010.station.util;

import java.sql.*;

public class DatabaseUtil {
    private static final String URL = "jdbc:mysql://127.0.0.1:3306/house?serverTimezone=GMT%2B8&characterEncoding=utf8";
    private static final String USER = System.getenv("MYSQL_USER");
    private static final String PWD = System.getenv("MYSQL_PASSWORD");
    private static Connection connection = null;

    static {
        try {
            Class.forName("com.mysql.cj.jdbc.Driver");
            connection = DriverManager.getConnection(URL, USER, PWD);
        } catch (ClassNotFoundException | SQLException e) {
            e.printStackTrace();
        }
    }

    public static Connection getConnection() {
        return connection;
    }
}
```

URL中的`127.0.0.1:3306`是MySQL服务所在的域名和端口，这里是本地的3306端口。`house`是数据库的名称，`serverTimezone`指定了数据库所在时区为`GMT+8`,`characterEncoding`指定了编码方式为`utf8`。使用 [utf-8](https://stackoverflow.com/questions/2241348/what-is-unicode-utf-8-utf-16) 可以避免中文乱码问题。USER和PASSWORD分别是MySQL的用户名和密码，这里通过环境变量的方式获取。

这里的`Class.forName()`使用反射的方式获取MySQL驱动类`com.mysql.cj.jdbc.Driver`，然后调用它的`getConnection()`方法获取数据库连接。

## 执行查询

下面我们使用上面定义的工具类从数据库中获取所有HouseView的记录。

```
public List<HouseView> getAllHouse() throws SQLException {
    Connection connection = DatabaseUtil.getConnection();
    String sql = "select h_id as id,u_id as owner,h_name as house,h_unit as unit,h_area as area,h_rent as rent,d_name as district,d_region as region,s_name as station,l_name as line from house_view";
    PreparedStatement statement = connection.prepareStatement(sql);
    ResultSet resultSet = statement.executeQuery();
    List<HouseView> houseViews = new ArrayList<>();
    while (resultSet.next()) {
        HouseView houseView = new HouseView();
        houseView.setId(resultSet.getInt("id"));
        houseView.setOwner(resultSet.getInt("owner"));
        houseView.setHouse(resultSet.getString("house"));
        houseView.setUnit(resultSet.getString("unit"));
        houseView.setArea(resultSet.getFloat("area"));
        houseView.setRent(resultSet.getFloat("rent"));
        houseView.setDistrict(resultSet.getString("district"));
        houseView.setRegion(resultSet.getString("region"));
        houseView.setStation(resultSet.getString("station"));
        houseView.setLine(resultSet.getString("line"));
        houseViews.add(houseView);
    }
    return houseViews;
}
```

我们首先以字符串的形式给出要执行的SQL语句，接着调用数据库连接的`prepareStatement()`方法预编译SQL语句，然后调用`executeQuery()`方法执行select语句并把返回值存入结果集。因为可能结果集中可能有多条记录，所以我们可以使用结果集的`next()`方法检查是否存在下一条记录并移动到下一条记录。最后，我们根据MySQL数据表和Java对象中的数据类型调用结果集中相应的get()方法获取结果集中的值并存入Java对象。

## 插入主键自增的记录

在开发中我们往往需要插入主键自增的记录，我们可以使用`RETURN_GENERATED_KEYS`来解决这个问题。

首先我们需要在MySQL的数据表定义中指定需要自增的主键为`auto_increment`：

```
create table house
(
   h_id                 int not null auto_increment,
   u_id                 int not null,
   d_id                 int not null,
   h_name               text,
   h_unit               varchar(10),
   h_area               float,
   h_rent               float,
   primary key (h_id)
);
```

然后通过结果集获取自增的主键：

```
public int insertHouse(House house) throws SQLException {
    Connection connection = DatabaseUtil.getConnection();
    String sql = "insert into house(u_id,d_id,h_name,h_unit,h_area,h_rent) values(?,?,?,?,?,?)";
    PreparedStatement statement = connection.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS);
    statement.setInt(1, house.getUser());
    statement.setInt(2, house.getDistrict());
    statement.setString(3, house.getName());
    statement.setString(4, house.getUnit());
    statement.setFloat(5, house.getArea());
    statement.setFloat(6, house.getRent());
    statement.executeUpdate();
    ResultSet resultSet = statement.getGeneratedKeys();
    if (resultSet.next()) {
        house.setId(resultSet.getInt(1));
        return house.getId();
    }
    else throw new SQLException("insert into house failed");
}
```

我们需要在SQL语句中用`?`表示未知的参数，接着根据MySQL数据表和Java对象中的数据类型调用SQL语句中相应的set()方法设置SQL语句的参数，然后调用`executeUpdate()`方法执行insert语句。最后通过`getGeneratedKeys()`方法获取自增的id并存入Java对象。

- [MySQL :: MySQL Connector/J 8.0 Developer Guide :: 4.2 Installing Connector/J Using Maven](https://dev.mysql.com/doc/connector-j/8.0/en/connector-j-installing-maven.html)
- [MySQL :: MySQL Connector/J 8.0 Developer Guide :: 7.2 Using JDBC Statement Objects to Execute SQL](https://dev.mysql.com/doc/connector-j/8.0/en/connector-j-usagenotes-statements.html)
- [MySQL :: MySQL Connector/J 8.0 Developer Guide :: 7.4 Retrieving AUTO_INCREMENT Column Values through JDBC](https://dev.mysql.com/doc/connector-j/8.0/en/connector-j-usagenotes-last-insert-id.html)