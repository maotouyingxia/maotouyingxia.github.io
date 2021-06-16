# 安装指南

在这个指南中我们将在Ubuntu中使用pip和venv来安装Scrapy。如果需要在其他平台上进行安装，请参考完整的[安装指南](https://docs.scrapy.org/en/latest/intro/install.html)。

## 安装pip

[pip](https://pypi.org/project/pip/)是一个Python包管理工具，它被用来安装和更新包。

Debian和大部分其他的发行版都包括一个[python-pip](https://packages.debian.org/stable/python-pip)包。你可以自己安装pip以确保你拥有最新的版本。建议使用系统pip在你的用户目录下安装pip。

`python3 -m pip install --user --upgrade pip`

在这之后，你的用户目录下应该安装有最新版本的pip。

```
xiangxin@DESKTOP-UK4BDMU:~$ python3 -m pip --version
pip 21.1.2 from /home/xiangxin/.local/lib/python3.8/site-packages/pip (python 3.8)
```

## venv相关

[venv](https://docs.python.org/3/library/venv.html)允许你为不同的项目分别安装它们各自需要的包。它本质上是让你创建一个“虚拟”的Python环境并把包安装到虚拟环境中。当你切换项目时，你可以简单地创建一个虚拟环境而不用担心影响其他环境中安装的包。

### 创建虚拟环境

为了创建虚拟环境，进入你的项目目录并运行venv。

`python3 -m venv env`

其中参数`env`是创建虚拟环境的位置。通常你只需要把它放在项目目录下并命名为`env`。venv会在`env`目录下创建一个虚拟的Python环境。你应当使用类似于`.gitignore`的方式把虚拟环境文件排除在版本控制系统之外。

### 激活虚拟环境

在你开始安装和使用包之前需要先激活虚拟环境。激活虚拟环境其实就是把虚拟环境中的`python`和`pip`可执行文件加入到shell的`PATH`中。

`source env/bin/activate`

你可以通过查看Python解释器的位置来检查是否已激活虚拟环境，它应该在`env`目录下。

```
(env) xiangxin@DESKTOP-UK4BDMU:~/spider$ which python
/home/xiangxin/spider/env/bin/python
```

激活虚拟环境后，pip就会在虚拟环境中安装包，你就能在你的Python项目中导入并使用包。

### 退出虚拟环境

如果你需要切换项目或者离开虚拟环境，只需要执行：

`deactivate`

如果你想要重新进入虚拟环境，只需要参照上面有关激活虚拟环境的指令。并不需要重新创建虚拟环境。

## 安装Scrapy

Scrapy使用纯Python实现，它主要依赖于以下Python包：

- [lxml](https://lxml.de/index.html)，一个高效XML和HTML语法分析器
- [parsel](https://pypi.org/project/parsel/)，一个基于lxml编写的HTML/XML数据提取库
- [w3lib](https://pypi.org/project/w3lib/)，一个多目标的用于处理URL和网页编码的工具
- [twisted](https://twistedmatrix.com/trac/)，一个异步网络框架
- [cryptography](https://cryptography.io/en/latest/)和[pyOpenSSL](https://pypi.org/project/pyOpenSSL/)，处理各种网络层级的安全需求

在Ubuntu下安装Scrapy需要先安装以下依赖：

`sudo apt-get install python3 python3-dev python3-pip libxml2-dev libxslt1-dev zlib1g-dev libffi-dev libssl-dev`

- `python3-dev`，`zlib1g-dev`，`libxml2-dev`和`libxslt1-dev`是`lxml`必需的
- `libssl-dev`和`libffi-dev`是`cryptography`必需的

然后进入虚拟环境，使用pip安装Scrapy：

`pip install scrapy`

使用`pip show`检查Scrapy是否成功安装：

```
(env) ~/spider$ pip show Scrapy
Name: Scrapy
Version: 2.5.0
Summary: A high-level Web Crawling and Web Scraping framework
Home-page: https://scrapy.org
Author: Scrapy developers
Author-email: None
License: BSD
Location: /home/xiangxin/spider/env/lib/python3.8/site-packages
Requires: pyOpenSSL, itemloaders, queuelib, protego, parsel, w3lib, lxml, cryptography, cssselect, itemadapter, zope.interface, service-identity, h2, PyDispatcher, Twisted
Required-by:
```

- [Installation guide — Scrapy 2.5.0 documentation](https://docs.scrapy.org/en/latest/intro/install.html#intro-install)
- [Installing packages using pip and virtual environments — Python Packaging User Guide](https://packaging.python.org/guides/installing-using-pip-and-virtual-environments/)