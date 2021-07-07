# 分层设计

## 目录结构

在前面我们已经知道，Web服务中相同的部分可以由Tomcat完成，不同的部分需要使用Servlet完成。在开放中，许多东西会导致Servlet的实现变得臃肿，比如前面引入的数据库连接。我们可以使用分层设计解决这个问题。

通常，我们使用类似如下的方式组织源代码：

```
station
├─bean
├─controller
├─dao
├─service
└─util
```

- bean用于存放我们要操纵数据对象的类
- controller用于存放解析请求参数并返回处理结果的类
- dao用于存放访问数据库的类
- service用于存放实现具体业务逻辑的类
- util用于存放一些工具类

## 代码示例

下面给出一个实现`我的收藏`的例子：

Favorite.java

```
package com.comp3010.station.bean;

import lombok.Data;

import java.sql.Date;

@Data
public class Favorite {
    private int user;
    private int house;
    private Date date;
}
```

这里我们使用 [lombok](https://projectlombok.org/) 的`@Data`注解生成Getters和Setters方法。

FavoriteDao.java

```
package com.comp3010.station.dao;

import com.comp3010.station.bean.Favorite;

import java.sql.SQLException;
import java.util.List;

public interface FavoriteDao {
    int insertFavorite(Favorite favorite) throws SQLException;

    List<Favorite> getAllFavorite(int uid) throws SQLException;
}
```

在Dao层定义访问数据库的接口。

FavoriteService.java

```
package com.comp3010.station.service;

import com.comp3010.station.bean.Favorite;
import com.comp3010.station.dao.FavoriteDao;
import com.comp3010.station.util.DatabaseUtil;

import java.sql.*;
import java.util.*;

public class FavoriteService implements FavoriteDao {
    @Override
    public int insertFavorite(Favorite favorite) throws SQLException {
        Connection connection = DatabaseUtil.getConnection();
        String sql = "insert into favorite(h_id,u_id,f_date) values(?,?,?)";
        PreparedStatement statement = connection.prepareStatement(sql);
        statement.setInt(1, favorite.getHouse());
        statement.setInt(2, favorite.getUser());
        statement.setDate(3, favorite.getDate());
        return statement.executeUpdate();
    }

    @Override
    public List<Favorite> getAllFavorite(int uid) throws SQLException {
        Connection connection = DatabaseUtil.getConnection();
        String sql = "select h_id as house,u_id as user,f_date as date from favorite where u_id=?";
        PreparedStatement statement = connection.prepareStatement(sql);
        statement.setInt(1, uid);
        ResultSet resultSet = statement.executeQuery();
        List<Favorite> favorites = new ArrayList<>();
        while (resultSet.next()) {
            Favorite favorite = new Favorite();
            favorite.setHouse(resultSet.getInt("house"));
            favorite.setUser(resultSet.getInt("user"));
            favorite.setDate(resultSet.getDate("date"));
            favorites.add(favorite);
        }
        return favorites;
    }
}
```

因为收藏的逻辑比较简单，所以我们直接在Service中实现了Dao中的接口。在逻辑比较复杂时，可以在DaoImpl中实现Dao中的接口，然后在Service中调用DaoImpl以实现复杂功能。

FavoriteServlet.java

```
package com.comp3010.station.controller;

import com.comp3010.station.bean.Favorite;
import com.comp3010.station.service.FavoriteService;

import javax.servlet.annotation.WebServlet;
import javax.servlet.http.*;
import java.sql.Date;

@WebServlet("/FavoriteServlet")
public class FavoriteServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) {
        req.setCharacterEncoding("utf-8");
        String method = req.getParameter("method");
        if (method.equals("post")) {
            doPost(req, resp);
        } else {
            int uid = (int) req.getSession().getAttribute("uid");
            FavoriteService favoriteService = new FavoriteService();
            req.setAttribute("list", favoriteService.getAllFavorite(uid));
            req.getRequestDispatcher("favorite.jsp").forward(req, resp);
        }
    }

    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) {
        Favorite favorite = new Favorite();
        favorite.setHouse(Integer.parseInt(req.getParameter("hid")));
        favorite.setUser((int)req.getSession().getAttribute("uid"));
        favorite.setDate(Date.valueOf(req.getParameter("date")));
        FavoriteService favoriteService = new FavoriteService();
        favoriteService.insertFavorite(favorite);
        req.setAttribute("h1", "收藏成功");
        req.setAttribute("href", "HouseServlet?method=get");
        req.setAttribute("anchor", "返回首页");
        req.getRequestDispatcher("message.jsp").forward(req,resp);
    }
}
```

我们通过`method`参数判断请求的类型（插入收藏或查看收藏），并把处理后的数据加入请求并转发给相应的JSP文件生成响应。

- [代码结构中Dao，Service，Controller，Util，Model是什么意思，为什么划分？ - 知乎](https://www.zhihu.com/question/58410621/answer/157049250)