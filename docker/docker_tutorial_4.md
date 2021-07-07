# 部署应用

在实际的应用部署中，我们可能会使用到许多的镜像，如何协调这些镜像是个问题。下面我们以Django为例，使用docker-compose部署应用程序。

## 编写docker-compose文件

新建`docker-compose.yml`文件，我们将在这里编排需要使用的容器。其中`version`指定了`docker-compose.yml`文件的版本，这需要与你的Docker Engine的版本相对应。

### 数据库

```
version: '3.3'

services:
  db:
    image: mysql
    container_name: mysql
    command:
      --default-authentication-plugin=mysql_native_password
      --character-set-server=utf8mb4 
      --collation-server=utf8mb4_unicode_ci
    environment:
      - MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}
      - MYSQL_DATABASE=findshow
      - MYSQL_USER=${MYSQL_USER}
      - MYSQL_PASSWORD=${MYSQL_PASSWORD}
    volumes:
      - data:/var/lib/mysql
    ports:
      - 3306:3306

volumes:
  data:
```

`db`定义了数据库服务的名称。

`image`指定了使用的镜像。

`container_name`指定了容器的名称。

`command`中的选项为不支持 [caching_sha2_password](https://dev.mysql.com/doc/refman/8.0/en/caching-sha2-pluggable-authentication.html) 验证方式的数据库驱动保留了mysql_native_password的认证方式，并指定使用utf8mb4字符集。

`environment`中定义了一系列环境变量。

数据卷（volumes）是Docker持久化数据的一种方式，在这里我们定义了名为`data`的数据卷并把MySQL的数据映射到它。你可以使用`docker volume`命令管理数据卷，比如`docker volume ls`查看所有数据卷，`docker volume prune`移除未使用的数据卷。

`ports`将虚拟机的`3306`端口映射到宿主机的`3306`端口。

### 应用服务器

```
version: '3.3'

services:
  web:
    image: maotouyingxia/findshow
    container_name: django
    command: bash -c "
      wait-for-it db:3306 -t 60
      && python3 manage.py makemigrations 
      && python3 manage.py migrate 
      && gunicorn findshow.wsgi:application --bind 0.0.0.0:8000"
    expose: 
      - "8000"
    environment: 
      - SECRET_KEY=${SECRET_KEY}
      - MYSQL_USER=${MYSQL_USER}
      - MYSQL_PASSWORD=${MYSQL_PASSWORD}
    depends_on: 
      - db
```

因为Django应用程序依赖于MySQL数据库，所以如果Django先于MySQL完成初始化并向仍在初始化的MySQL发起连接便会发生错误。仅通过`depends_on`指明服务的依赖并不能解决这个问题，因为`depends_on`只能决定容器的启动和停止顺序，并不能保证MySQL在Django之前完成初始化。

比较好的解决方案是使用 [wait-for-it](https://tracker.debian.org/pkg/wait-for-it) ，许多发行版都包含这个包，建议在构建镜像时使用`apt-get`预先安装这个包。`wait-for-it`命令可以保持等待直到指定的主机上指定的端口可用，可以使用`-t`选项指定超时时间。可以使用服务的名字直接作为主机名，比如这里的`db`。

在这里，我们保持等待直至MySQL服务可用，然后执行数据库迁移的相关命令，最后使用 [gunicorn](https://gunicorn.org/) 运行Django应用程序。

可以使用`bash -c`和`&&`执行多条命令。

使用`expose`暴露`8000`端口。

### Web服务器

```
upstream web {
    ip_hash;
    server web:8000;
}

server {
    listen 80;
    server_name localhost;

    location / {
        proxy_pass http://web/;
    }
}
```

在`nginx`目录下编写Nginx配置文件`nginx.conf`并使用数据卷映射到容器的Nginx配置文件中。

```
version: '3.3'

services:
  nginx:
    image: maotouyingxia/nginx
    container_name: nginx
    ports: 
      - 80:80
    volumes: 
      - ./nginx:/etc/nginx/conf.d
    command: bash -c "
      wait-for-it web:8000 -t 60
      && nginx -g 'daemon off;'"
    depends_on: 
      - web
```

因为Docker容器会在前台进程执行完毕后退出，所以这里需要使用命令`nginx -g 'daemon off;'`在前台运行Nginx。

- [Quickstart: Compose and Django](https://docs.docker.com/samples/django/)
- [Control startup and shutdown order in Compose](https://docs.docker.com/compose/startup-order/)