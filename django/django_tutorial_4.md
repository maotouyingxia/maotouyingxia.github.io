# 全文检索

在这个教程中，我们将通过[Haystack](http://haystacksearch.org/)框架调用[Elasticsearch](https://www.elastic.co/cn/)服务实现演出信息的全文检索。Elasticsearch是广受欢迎的开源搜索解决方案。而Haystack为Django提供了模块化的搜索，它支持的搜索引擎有[Solr](http://lucene.apache.org/solr)、[Elasticsearch](http://elasticsearch.org/)、[Whoosh](http://whoosh.ca/)以及[Xapian](http://xapian.org/)。

## 安装

使用pip安装haystack：

`pip install django-haystack`

如果你使用Elasticsearch，还需要安装：

`pip install elasticsearch==5`

目前Haystack支持Elasticsearch 1.x、2.x以及5.x。有关安装Elasticsearch的部分，可以参考[下载Elasticsearch](https://www.elastic.co/downloads/elasticsearch)。如果你使用Docker，你可以使用下面的命令拉取镜像：

`docker pull elasticsearch:5.0`

创建自定义网络：

`docker network create somenetwork`

运行Elasticsearch服务：

`docker run -d --name elasticsearch --net somenetwork -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:5.0`

如果你使用其他的搜索引擎，可以参考[Installing Search Engines](https://django-haystack.readthedocs.io/en/master/installing_search_engines.html)

## 配置

### 把Haystack添加到`INSTALLED_APPS`

与许多Django应用一样，你应该把Haystack加入到配置文件（通常是`settings.py`）中的`INSTALLED_APPS`中：

```
INSTALLED_APPS = [
    'search.apps.SearchConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'haystack',
]
```

### 修改你的`settings.py`

在你的`settings.py`中，你还需要指定使用的搜索引擎并完成相应的配置，以下以Elasitcsearch 5.x为例：

```
HAYSTACK_CONNECTIONS = {
    'default': {
        'ENGINE': 'haystack.backends.elasticsearch5_backend.Elasticsearch5SearchEngine',
        'URL': 'http://localhost:9200/',
        'INDEX_NAME': 'haystack',
    },
}
```

如果你使用其他的版本或其他的搜索引擎，可以参考[Modify Your settings.py](https://django-haystack.readthedocs.io/en/master/tutorial.html#modify-your-settings-py)。

## 处理数据

### 创建`SearchIndexes`

Haystack通过`SearchIndex`对象决定哪些数据应该被放到搜索引擎的索引中并使用它们处理输入的数据流。你可以认为它们与Django的`Models`或者`Forms`类似，因为它们都基于域变量（Field）并用于操纵和存储数据。

为了创建`SearchIndex`，你需要继承`indexes.SearchIndex`和`indexes.Indexable`并定义你想要用于存储数据的域（Field）以及一个`get_model`方法。

下面我们在search应用的目录下新建文件`search_indexes.py`，对应于Show模型创建下面的`ShowIndex`：

```
from datetime import datetime
from haystack import indexes
from search.models import Show

class ShowIndex(indexes.SearchIndex, indexes.Indexable):
    text = indexes.CharField(document=True, use_template=True)
    time = indexes.DateTimeField(model_attr="time")
    
    def get_model(self):
        return Show

    def index_queryset(self, using=None):
        """Used when the entire index for model is updated."""
        return self.get_model().objects.filter(time__gte=datetime.now())
```

每一个`SearchIndex`都需要（且只能）指定一个域（Field）为`document=True`，这个域是Haystack和搜索引擎在搜索中的首要的域（Field）。通常这个域的名称是`text`，如果你想使用其他的名字，则需要在`settings.py`中配置`HAYSTACK_DOCUMENT_FIELD`为你指定的名字。

除此之外，我们还指定`text`域为`use_template=True`。这允许我们使用一个数据模板来建立搜索引擎要索引的文档。你需要在你的`template`目录创建一个新的模板`findshow/search/templates/search/indexes/search/show_text.txt`：

```
{{ object.title }}
{{ object.venue }}
{{ object.artist }}
```

我们还添加了`time`域，这个域在`index_queryset`中被用作过滤过时的演出信息。

## 建立视图

### 把`SearchView`添加到URLconf

在`findshow/urls.py`的`urlpatterns`中添加Haystack的视图：

`url(r'^search/', include('haystack.urls')),`

### 搜索模板

在`findshow/search/templates/search`下参考[search template](https://django-haystack.readthedocs.io/en/master/tutorial.html#search-template)创建模板文件`search.html`。

注意到`page.object_list`实际上是`SearchResult`对象的列表而不是独立的模型。这些对象包括从记录中按照搜索引擎的索引和排序返回的所有数据。它们可以通过`{{ result.object }}`直接访问模型得到结果。所以`{{ result.object.title }}`使用了数据库中实际的`Show`对象并访问了它的`title`域。

### 重建索引

执行下面的命令为数据库中的数据建立索引：

`python manage.py rebuild_index`

使用标准的`SearchIndex`，你的索引只会在你执行`python manage.py rebuild_index`重建索引或者执行`python manage.py update_index`更新索引时才会更新。所以你应该配置一个更新的cron定时任务，有关定时任务的内容可以参考[使用 cron 调度任务](https://linux.cn/article-13383-1.html)。

当然，如果你的业务流量不大又或者你的搜索引擎能够地很好应付它们。你也可以考虑使用`RealtimeSignalProcessor`自动地处理更新和删除并即时更新索引。

### 完成

现在你可以访问你网站的搜索部分，输入一个查询并查看相应的搜索的结果。

- [Getting Started with Haystack — Haystack 2.5.0 documentation](https://django-haystack.readthedocs.io/en/master/tutorial.html)
- [elasticsearch](https://hub.docker.com/_/elasticsearch)