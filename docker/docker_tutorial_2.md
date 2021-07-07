# 基础教程

在这个教程中，我们以MySQL、Elasticsearch为例，在开发中使用Docker运行需要的服务。

## MySQL

使用以下命令运行一个简单的MySQL实例：

`docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag`

`docker run`命令用于创建一个新容器并执行一条命令。可以使用命令`docker ps -a`查看所有的容器。其中`--name`选项指定了容器的名字为`some-mysql`，`-e`选项指定了容器的环境变量`MYSQL_ROOT_PASSWORD`为`my-secret-pw`，即这个MySQL实例的超级用户密码。`tag`用于指定MySQL实例的版本，如`5.7`、`8.0`。

你也可以使用`docker stack deploy`或者`docker-compose`：

`stack.yml`

```
version: '3.1'

services:

  db:
    image: mysql
    command: --default-authentication-plugin=mysql_native_password
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: example

  adminer:
    image: adminer
    restart: always
    ports:
      - 8080:8080
```

执行命令`docker stack deploy -c stack.yml mysql`或者`docker-compose -f stack.yml up`启动服务。

[adminer](https://www.adminer.org/) 是一个数据库管理工具。容器初始化完成后，访问 http://localhost:8080 ，你可以在adminer提供的图形界面连接并管理你的MySQL数据库。

你还可以在`environment`中设置数据库`MYSQL_DATABASE`、用户名`MYSQL_USER`、密码`MYSQL_PASSWORD`等其他环境变量。

## Elasticsearch

创建自定义网络：

`docker network create somenetwork`

在Docker中，同一个网络下不同容器中运行的服务可以相互通信，比如 [Kibana](https://www.elastic.co/cn/kibana) 。

运行Elasticsearch实例：

`docker run -d --name elasticsearch --net somenetwork -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:tag`

其中选项`--net`指定了容器所属的网络，选项`-p`指定了服务运行的端口。容器初始化完成后可以访问`http://localhost:9200/?pretty`或者使用命令`curl http://localhost:9200/?pretty`进行验证。

- [mysql](https://hub.docker.com/_/mysql)
- [elasticsearch](https://hub.docker.com/_/elasticsearch)