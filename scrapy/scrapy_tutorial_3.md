# 保存到数据库

在这个教程中，我们将使用[PyMySQL](https://github.com/PyMySQL/PyMySQL/)连接到[MySQL](https://www.mysql.com/)数据库，并把爬虫提取的数据保存到数据库中。PyMySQL是基于[PEP 249](https://www.python.org/dev/peps/pep-0249/)使用纯Python实现MySQL客户端程序库。

## 定义数据对象

在Scrapy中，`item`类似于字典，是`scrapy.Item`的子类，可以使用简单的类定义语法和`Field`对象来定义。下面在`showspider/items.py`中，定义我们要处理的数据对象：

```
# Define here the models for your scraped items
#
# See documentation in:
# https://docs.scrapy.org/en/latest/topics/items.html

import scrapy


class ShowspiderItem(scrapy.Item):
    # define the fields for your item here like:
    # name = scrapy.Field()
    id = scrapy.Field()
    title = scrapy.Field()
    url = scrapy.Field()
    date = scrapy.Field()
    time = scrapy.Field()
    venue = scrapy.Field()
    artist = scrapy.Field()
    source = scrapy.Field()
```

我们可以注意到Scrapy Item的定义类似于[Django Models](https://docs.djangoproject.com/en/dev/topics/db/models/)，不过因为没有不同的field类型的概念，Scrapy Item要简单得多。

## 定义数据表

对应于Scrapy Item，在MySQL数据库中创建相应的数据表。

```
CREATE TABLE `search_show` (
    `id` bigint NOT NULL PRIMARY KEY, 
    `title` longtext NOT NULL, 
    `url` varchar(50) NOT NULL, 
    `date` varchar(30) NOT NULL, 
    `time` datetime(6) NOT NULL, 
    `venue` longtext NOT NULL, 
    `artist` longtext NOT NULL, 
    `source` varchar(10) NOT NULL
);
```

## 启用流水线

在Scrapy中，一个item在被爬虫提取出来后，会被送到Item Pipeline，在这里它将会被多个流水线按顺序先后处理。

每条流水线都是实现了一个简单方法的Python类。它们接收item并对其执行操作，并决定其是否应该送入下一条流水线还是丢弃且不再处理。

在`showspider/pipelines.py`中编写流水线：

```
# Define your item pipelines here
#
# Don't forget to add your pipeline to the ITEM_PIPELINES setting
# See: https://docs.scrapy.org/en/latest/topics/item-pipeline.html


# useful for handling different item types with a single interface
from itemadapter import ItemAdapter


class ShowspiderPipeline:
    def process_item(self, item, spider):
        return item
```

在`showspider/settings.py`中启用流水线：

```
# Configure item pipelines
# See https://docs.scrapy.org/en/latest/topics/item-pipeline.html
ITEM_PIPELINES = {
   'showspider.pipelines.ShowspiderPipeline': 300,
}
```

## 连接到数据库

在`ShowspiderPipeline`类的`__init__`方法中连接到MySQL数据库：

```
import pymysql.cursors
from showspider import settings

...

    def __init__(self) -> None:
        self.connection = pymysql.connect(
            host=settings.MYSQL_HOST,
            user=settings.MYSQL_USER,
            password=settings.MYSQL_PASSWORD,
            database=settings.MYSQL_DATABASE,
            charset='utf8mb4',
            cursorclass=pymysql.cursors.DictCursor)
```

在`showspider/settings.py`配置MySQL的域名、用户、密码和数据库：

```
import os

# MySQL Connection
MYSQL_HOST = 'db'
MYSQL_DATABASE = 'findshow'
MYSQL_USER = os.environ['MYSQL_USER']
MYSQL_PASSWORD = os.environ['MYSQL_PASSWORD']
```

## 持久化数据

在`ShowspiderPipeline`类的`process_item`方法中持久化数据：

```
import logging

...

    def process_item(self, item, spider):
            self.connection.ping(reconnect=True)
            with self.connection as conn:
                with conn.cursor() as cursor:
                    try:
                        sql = "select * from search_show where id = %s"
                        cursor.execute(sql, (item["id"],))
                        result = cursor.fetchone()
                        if result is not None:
                            sql = """update search_show set title=%s,url=%s,date=%s,
                                time=%s,venue=%s,artist=%s,source=%s where id=%s"""
                            cursor.execute(sql, (item['title'], item['url'], item['date'], 
                                item['time'], item['venue'], item['artist'], item['source'], item['id']))
                        else:
                            sql = """insert into search_show(id,title,url,date,time,venue,artist,source)
                                value (%s,%s,%s,%s,%s,%s,%s,%s)"""
                            cursor.execute(sql, (item['id'], item['title'], item['url'], 
                                item['date'], item['time'], item['venue'], item['artist'], item['source']))
                        conn.commit()
                    except Exception as e:
                        logging.error(e)
                    return item
```

使用`self.connection.ping(reconnect=True)`保持与数据库的连接。在这里我们根据id查找演出是否存在，若存在则更新演出信息，若不存在则插入演出信息。最后使用`conn.commit()`提交执行的修改。

- [Items — Scrapy 2.5.0 documentation](https://docs.scrapy.org/en/latest/topics/items.html)
- [Item Pipeline — Scrapy 2.5.0 documentation](https://docs.scrapy.org/en/latest/topics/item-pipeline.html)
- [PyMySQL · PyPI](https://pypi.org/project/PyMySQL/)