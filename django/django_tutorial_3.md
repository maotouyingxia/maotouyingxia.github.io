# 创建模型

## 数据库配置

打开`findshow/settings.py`，这是个包含Django项目设置的Python模块。通常，这个配置文件使用 [SQLite](https://sqlite.org/index.html) 作为默认数据库。Python内置SQLite，所以你无需安装额外的东西来使用它。但当你开始一个真正的项目时，你可能更倾向使用一个更具扩展性的数据库，例如 [PostgreSQL](https://www.postgresql.org/) 。

如果你想使用其他的数据库，你需要安装合适的数据库驱动，然后改变配置文件中`DATABASES 'default'`项目中的一些键值，以MySQL数据库为例，你需要安装 [mysqlclient](https://pypi.org/project/mysqlclient/) 并在`settings.py`中配置如下内容：

```
import os

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'findshow',
        'USER': os.environ['MYSQL_USER'],
        'PASSWORD': os.environ['MYSQL_PASSWORD'],
        'HOST': 'localhost',
        'POST': '3306'
    }
}
```

其中`NAME`是数据库的名称，`USER`是用户名，`PASSWORD`是密码，`HOST`是数据库服务的域名，`POST`是数据库服务的端口。

使用以下命令在MySQL中创建相应的数据库：

`CREATE DATABASE findshow;`

`settings.py`中的`INSTALLED_APPS`设置项包括了项目中启用的所有Django应用，通常`INSTALLED_APPS`包括以下Django的自带应用：

- [django.contrib.admin](https://docs.djangoproject.com/zh-hans/3.2/ref/contrib/admin/#module-django.contrib.admin) 管理员站点
- [django.contrib.auth](https://docs.djangoproject.com/zh-hans/3.2/topics/auth/#module-django.contrib.auth) 认证授权系统
- [django.contrib.contenttypes](https://docs.djangoproject.com/zh-hans/3.2/ref/contrib/contenttypes/#module-django.contrib.contenttypes) 内容类型框架
- [django.contrib.sessions](https://docs.djangoproject.com/zh-hans/3.2/topics/http/sessions/#module-django.contrib.sessions) 会话框架
- [django.contrib.messages](https://docs.djangoproject.com/zh-hans/3.2/ref/contrib/messages/#module-django.contrib.messages) 消息框架
- [django.contrib.staticfiles](https://docs.djangoproject.com/zh-hans/3.2/ref/contrib/staticfiles/#module-django.contrib.staticfiles) 管理静态文件的框架

默认开启的某些应用需要至少一个数据表，所以，在使用他们之前需要在数据库中创建一些表。执行以下命令：

`python manage.py migrate`

这个`migrate`命令检查`INSTALLED_APPS`设置，为其中每个应用创建需要的数据表。

## 创建模型

在这个搜索应用中，需要创建的模型是演出（Show），Show模型包括演出的时间、地点、艺人等信息。这些概念可以通过一个Python类来描述，在`search/models.py`中定义模型：

```
from django.db import models

# Create your models here.
class Show(models.Model):
    id = models.BigIntegerField(primary_key=True)
    title = models.TextField()
    url = models.URLField(max_length=50)
    date = models.CharField(max_length=30)
    time = models.DateTimeField()
    venue = models.TextField()
    artist = models.TextField()
    source = models.CharField(max_length=10)
```

每个模型被表示为`django.db.models.Model`类的子类。每个模型有许多类变量，它们都表示模型里的一个数据库字段。

每个字段都是`Field`类的实例，比如字符字段`CharField`,日期时间字段`DateTimeField`。这将告诉Django每个字段要处理的数据类型。

每个`Field`类实例变量的名字（例如time或venue）也是字段名，所以最好使用机器友好的格式。你将会在Python代码里使用它们，数据库也会将它们作为列名。

定义某些`Field`类实例需要参数。例如`CharField`需要一个`max_length`参数。而`primary_key=True`则使用这一列作为主键。

## 激活模型

用于创建模型的代码给了Django很多信息，通过这些信息，Django可以：
- 为这个应用创建数据库的模式（生成CREATE TABLE语句）
- 创建可以与Show对象进行交互的Python数据库API

不过我们首先需要把`search`应用安装到我们的项目里。

为了在我们的工程中包含整个应用，我们需要在配置类`INSTALLED_APPS`中添加设置。因为`SearchConfig`类写在文件`polls/apps.py`中，所以它的点式路径是`search.apps.PollsConfig`。在文件`findshow/settings.py`中`INSTALLED_APPS`子项添加点式路径：

```
INSTALLED_APPS = [
    'search.apps.SearchConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
]
```

现在你的Django项目会包含`search`应用。运行下面的命令：

`python manage.py makemigrations search`

你将会看到类似于下面的输出：

```
Migrations for 'search':
  search/migrations/0001_initial.py
    - Create model Show
```

通过运行`makemigrations`命令，Django会检测你对模型文件的修改，并把修改的部分储存为一次迁移。

迁移是Django对于模型定义变化的储存形式，它们是被存储在`search/migrations/0001_initial.py`里的文件。我们可以使用`sqlmigrate`命令查看迁移要执行的SQL语句：

```
--
-- Create model Show
--
CREATE TABLE `search_show` (`id` bigint NOT NULL PRIMARY KEY, `title` longtext NOT NULL, `url` varchar(50) NOT NULL, `date` varchar(30) NOT NULL, `time` datetime(6) NOT NULL, `venue` longtext NOT NULL, `artist` longtext NOT NULL, `source` varchar(10) NOT NULL);
```

`sqlmigrate`命令并不会在数据库中执行迁移，它只是把命令输出到屏幕上，让你看看Django认为需要执行哪些SQL语句。下面执行`migrate`命令，在数据库中创建定义的模型和数据表:

`python manage.py migrate`

这个`migrate`命令选中所有还没有执行过的迁移并应用再数据库上，即把你对模型的更改同步到数据库结构上。

## 初试API

现在让我们进入Python shell，尝试一下Django为你创建的各种API：

`python manage.py shell`

这里的`manage.py`会设置`DJANGO_SETTINGS_MODULE`环境变量，这个变量会让Django根据`mysite/settings.py`文件来设置Python包的导入路径。

```
# 导入模型
>>> from search.models import Show
# 现在还没有演出
>>> Show.objects.all()
<QuerySet []>
# 创建一个新的演出
>>> import datetime
>>> s = Show(id=1, title="show tonight", time=datetime.datetime.now())
# 把演出保存到数据库
>>> s.save()
# 通过Python属性查看演出的信息
>>> s.id
1
>>> s.title
'show tonight'
>>> s.time
datetime.datetime(2021, 6, 15, 13, 10, 36, 732051)
# 通过改变属性改变值然后保存
>>> s.title = "show now."
>>> s.save()
# 展示数据库中所有的演出
>>> Show.objects.all()
<QuerySet [<Show: Show object (1)>]>
```

`<Show: Show object (1)>`对我们了解演出的细节没有什么帮助，我们可以通过再`polls/models.py`修改`Show`模型来解决这个问题：

```
class Show(models.Model):
    ...

    def __str__(self):
        return self.title
```

## Django管理页面

Django可以全自动地根据模型创建一个用户可以添加、修改和删除内容的后台界面。

### 创建管理员账号

执行以下命令创建一个能登录管理页面的用户：

`python manage.py createsuperuser`

输入用户名：

`Username: admin`

输入邮件地址：

`Email address: admin@example.com`

输入并确认密码：

```
Password: **********
Password (again): *********
Superuser created successfully.
```

### 启动开发服务器

执行以下命令启动开发服务器：

`python manage.py runserver`

在浏览器打开 http://127.0.0.1:8000/admin ，你应该会看见管理员登录界面。

### 进入管理站点页面

使用你在前面创建的超级用户登录，然后你应该会看见Django管理页面的索引页。在这里你能看到可编辑的组和用户，它们是由 [django.contrib.auth](https://docs.djangoproject.com/zh-hans/3.2/topics/auth/#module-django.contrib.auth) 提供的，这是Django的认证框架。

### 向管理页面中加入搜索应用

现在在索引页并没有显示我们的搜索应用，所以我们需要告诉管理应用演出Show对象需要一个后台接口。编辑`search/admin.py`文件：

```
from django.contrib import admin
from .models import Show

# Register your models here.
admin.site.register(Show)
```

### 便捷的管理功能

现在你应该能在索引也看到search应用以及Show模型。在这里你可以方便地添加，删除以及修改演出。

- [编写你的第一个 Django 应用，第 2 部分](https://docs.djangoproject.com/zh-hans/3.2/intro/tutorial02/)