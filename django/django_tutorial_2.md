# 基础教程

在这个教程中我们将创建一个演出搜索网站。

## 创建项目

在你想要创建Scrapy项目的目录下执行命令：

`django-admin startproject findshow`

这行代码会在当前目录下创建一个findshow目录，这个目录包括如下内容：

```
findshow/
    manage.py       # 管理项目的命令行工具
    findshow/       # 一个名为findshow的Python包
        __init__.py
        settings.py # 项目的配置文件
        urls.py     # 项目的URL声明
        asgi.py     # 在ASGI兼容的Web服务器上的入口
        wsgi.py     # 在WSGI兼容的Web服务器上的入口
```

## 用于开发的简易服务器

执行命令`python manage.py runserver`启动Django自带的用于开发的简易服务器：

```
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 18 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
June 15, 2021 - 11:05:37
Django version 3.2.4, using settings 'findshow.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

我们会在稍后处理有关未应用最新数据库迁移的警告。使用浏览器访问 http://127.0.0.1:8000，你应该能看到一个“祝贺”页面，随着一只火箭发射，服务器已经开始运行。

默认情况下，`runserver`命令会将服务器设置未监听本机内部IP的8000端口，可以使用命令行参数指定服务器的监听端口：

`python manage.py runserver 8080`

这个用于开发的服务器在需要的情况下会为每一次的访问请求重新载入Python代码。所以你不需要频繁地重启服务器以让修改的代码生效。但是一些动作并不会触发自动重新加载，比如添加新的文件，这时你需要手动重启服务器。

## 创建搜索应用

执行命令`python manage.py startapp search`创建搜索应用，这行代码将会创建一个`search`目录，这个目录包括以下内容：

```
search/
    __init__.py
    admin.py
    apps.py
    migrations/
        __init__.py
    models.py
    tests.py
    views.py
```

## 编写视图

打开`search/views.py`输入以下代码：

```
from django.shortcuts import render
from django.http import HttpResponse

# Create your views here.
def index(request):
    return HttpResponse("Hello, you're at search index.")
```

这是Django中最简单的视图，但要看到效果，我们还需要将一个URL映射到它。在`search`目录下新建一个`urls.py`文件，输入以下内容创建search的URLconf：

```
from django.urls import path
from . import views

urlpatterns = [
    path('', views.index, name='index'),
]
```

我们还需要在根URLconf文件中指定刚刚创建的`polls.urls`模块。在`findshow/urls.py`文件的`urlpatterns`列表里插入一个`include()`：

```
"""findshow URL Configuration

The `urlpatterns` list routes URLs to views. For more information please see:
    https://docs.djangoproject.com/en/3.2/topics/http/urls/
Examples:
Function views
    1. Add an import:  from my_app import views
    2. Add a URL to urlpatterns:  path('', views.home, name='home')
Class-based views
    1. Add an import:  from other_app.views import Home
    2. Add a URL to urlpatterns:  path('', Home.as_view(), name='home')
Including another URLconf
    1. Import the include() function: from django.urls import include, path
    2. Add a URL to urlpatterns:  path('blog/', include('blog.urls'))
"""
from django.contrib import admin
from django.urls import path
from django.urls.conf import include

urlpatterns = [
    path('search/', include('search.urls')),
    path('admin/', admin.site.urls),
]
```

函数`include()`允许引用其他URLconfs。每当Django遇到`include()`时，它会截断与此项匹配的URL部分，并将余下的字符串发送给URLconf做进一步处理。

现在我们已经把`search`视图添加进了URLconf。运行服务器，用浏览器访问 http://localhost:8000/search ，你应该能看到你在search视图里定义的`Hello, you're at search index.`。

- [编写你的第一个 Django 应用，第 1 部分 | Django 文档 | Django](https://docs.djangoproject.com/zh-hans/3.2/intro/tutorial01/)