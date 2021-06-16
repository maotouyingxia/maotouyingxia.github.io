# 基础教程

在这个教程中，我们将爬取[秀动网](https://www.showstart.com/)，这是一个音乐现场票务网站。

## 创建项目

在你想要创建Scrapy项目的目录下执行命令：

`scrapy startproject showspider`

这将创建一个包含如下内容的`showspider`目录：

```
scrapy.cfg            # 用于部署的配置文件

showspider/             # 项目目录
    __init__.py

    items.py          # 提取的数据对象

    middlewares.py    # 项目的中间件

    pipelines.py      # 项目的流水线

    settings.py       # 项目配置文件

    spiders/          # 存放爬虫的目录
        __init__.py
```

items.py是为要提取数据对象建立的类。middleware是为一些中间件建立的类，中间件能够处理爬虫运行中的输入输出、请求以及异常。pipeline是为数据处理流水线建立的类，流水线能够完成对爬取数据的处理。

## 创建爬虫

Scrapy根据你定义的爬虫从网站上提取信息。它们都必须继承`scrapy.Spider`并定义要发起的初始请求，你还可以定义如何追踪网页上的链接，如何分析下载的网页内容以提取数据。

以下是我们第一个爬虫的代码，把它作为一个名为`showstart.py`的文件保存到`showspider/spiders`目录下：

```
import scrapy


class ShowstartSpider(scrapy.Spider):
    name = "showstart"

    def start_requests(self):
        urls = [
            'https://www.showstart.com/',
        ]
        for url in urls:
            yield scrapy.Request(url=url, callback=self.parse)

    def parse(self, response):
        filename = 'showstart.html'
        with open(filename, 'wb') as f:
            f.write(response.body)
        self.log(f'Saved file {filename}')
```

如你所见，我们的爬虫继承了`scrapy.Spider`并定义了一些属性和方法：
- `name`: 爬虫的名字
- `start_urls`: 初始的请求，必须是可迭代的请求（请求的列表或者请求生成器）
- `parse()`: 一个用来处理各个请求下载的响应内容的方法。`response`参数是`TextResponse`的一个实例，它包含网页的内容和一些用于处理网的有用方法。

`parse()`方法一般用来分析响应的内容，提取数据并存入字典，追踪链接并发起新的请求。

## 运行爬虫

在项目目录下执行命令`scrapy crawl showstart`，这个命令将启动名为`showstart`的爬虫，你可以得到类似如下的输出：

```
2021-06-13 17:35:04 [scrapy.core.engine] INFO: Spider opened
2021-06-13 17:35:04 [scrapy.extensions.logstats] INFO: Crawled 0 pages (at 0 pages/min), scraped 0 items (at 0 items/min)
2021-06-13 17:35:04 [scrapy.extensions.telnet] INFO: Telnet console listening on 127.0.0.1:6023
2021-06-13 17:35:05 [scrapy.core.engine] DEBUG: Crawled (404) <GET https://www.showstart.com/robots.txt> (referer: None)
2021-06-13 17:35:05 [scrapy.core.engine] DEBUG: Crawled (200) <GET https://www.showstart.com/> (referer: None)
2021-06-13 17:35:05 [showstart] DEBUG: Saved file showstart.html
2021-06-13 17:35:05 [scrapy.core.engine] INFO: Closing spider (finished)
```

检查项目目录下的文件，你应该会发现爬虫下载的文件`showstart.html`，其中保存了秀动网首页的内容。

## 提取数据

下面我们在Scrapy shell中使用选择器来提取数据。执行命令`scrapy shell "https://www.showstart.com/"`进入scrapy shell，你可以得到类似如下的输出：

```
2021-06-13 19:16:24 [scrapy.core.engine] DEBUG: Crawled (200) <GET https://www.showstart.com/> (referer: None)
[s] Available Scrapy objects:
[s]   scrapy     scrapy module (contains scrapy.Request, scrapy.Selector, etc)
[s]   crawler    <scrapy.crawler.Crawler object at 0x7fb37ed650d0>
[s]   item       {}
[s]   request    <GET https://www.showstart.com/>
[s]   response   <200 https://www.showstart.com/>
[s]   settings   <scrapy.settings.Settings object at 0x7fb37ed60bb0>
[s]   spider     <DefaultSpider 'default' at 0x7fb37ea64ee0>
[s] Useful shortcuts:
[s]   fetch(url[, redirect=True]) Fetch URL and update local objects (by default, redirects are followed)
[s]   fetch(req)                  Fetch a scrapy.Request and update local objects
[s]   shelp()           Shell help (print this help)
[s]   view(response)    View response in a browser
```

在shell中，你可以试着使用CSS选择响应对象的元素：

```
>>> response.css('title')
[<Selector xpath='descendant-or-self::title' data='<title>秀动网（showstart.com） - 和热爱音乐的朋友开...'>]
```

返回的结果是一种被称为`SelectorList`的类似于列表的对象，它是一个包括XML/HTML元素的`Selector`对象的列表，可用于执行进一步查询以优化选择或者提取数据。

要提取上面title中的文字，你可以这样做：

```
>>> response.css('title::text').getall()
['秀动网（showstart.com） - 和热爱音乐的朋友开启原创音乐现场之旅']
```

我们在CSS查询中增加了`::text`，表明我们想要只选择`<title>`元素里的文字。如果我们不用`::text`明确说明这一点，则会得到整个title元素，包括它的标签：

```
>>> response.css('title').getall()
['<title>秀动网（showstart.com） - 和热爱音乐的朋友开启原创音乐现场之旅</title>']
```

调用`.getall()`返回的结果是一个列表：有可能一个选择器会返回多个结果，所以把它们全都提取出来。当你只想要第一个结果时，你可以这样做：

```
>>> response.css('title::text').get()
'秀动网（showstart.com） - 和热爱音乐的朋友开启原创音乐现场之旅'
```

或者是：

```
>>> response.css('title::text')[0].get()
'秀动网（showstart.com） - 和热爱音乐的朋友开启原创音乐现场之旅'
```

但是直接在`SelectorList`实例上使用`.get()`能够避免`IndexError`并在找不到任何能匹配选择条件的元素时返回`None`。

除了`getall()`和`get()`方法，你可以通过`re()`方法使用正则表达式：

```
>>> response.css('title::text').re(r'（(.+?)）')
['showstart.com']
```

为了能找到合适的CSS选择器，你也许需要使用`view(response)`在浏览器中打开响应的页面，然后使用浏览器的开发者工具检查HTML并得到需要的选择器（参见[使用浏览器的开发者工具提取数据]）。

## 提取演出信息

使用浏览器的检查工具，我们可以发现在 https://www.showstart.com/ 中，演出是以类似如下的HTML元素表示的：

```
<a href="/event/91204" class="activity-item image-scale">
    <div class="el-image"><img
            src="https://s2.showstart.com/img/2020/20200405/af132ebd5e764a07b57c3a21b31c4832_600_800_106992.0x0.jpg?imageMogr2/thumbnail/!204x272r/gravity/Center/crop/!204x272"
            class="el-image__inner" style="object-fit: cover;">
        <!---->
    </div>
    <div class="name">#展览#北京奇葩减压馆·保龄球·射箭·分娩体验·蹦床·星空水床·团建·发泄·摔碗一站畅玩 </div>
    <p>¥88<span>起</span></p>
</a>
```

接下来我们试着在Scrapy shell中提取想要的信息：

使用下面的方法得到表示演出的HTML元素的选择器列表：

```
>>> response.css('a.activity-item')
[<Selector xpath="descendant-or-self::a[@class and contains(concat(' ', normalize-space(@class), ' '), ' activity-item ')]" data='<a href="/event/91204" class="activit...'>, <Selector xpath="descendant-or-self::a[@class and contains(concat(' ', normalize-space(@class), ' '), ' activity-item ')]" data='<a href="/event/133984" class="activi...'>, ...]
```

上面的查询返回的每一个选择器都允许我们对它们的子元素执行进一步的查询。让我们把第一个选择器赋值给一个变量，以便直接对这个特定的演出上执行CSS选择器：

```
>>> show = response.css('a.activity-item')[0]
```

接下来，提取出`show`对象中的链接，名字和票价：

```
>>> show.css('a::attr(href)').get()
'/event/91204'
>>> show.css('div.name::text').get()
'#展览#北京奇葩减压馆·保龄球·射箭·分娩体验·蹦床·星空水床·团建·发泄·摔碗一站
畅玩 '
>>> show.css('p::text').get()
'¥88'
```

## 在爬虫中提取数据

让我们回到之前的爬虫。到目前为止，它并没有提取任何特定的数据，只是把整个HTML页面作为本地文件保存。现在我们把上面的提取逻辑整合到之前的爬虫中去。

Scrapy爬虫通常生成许多字典用来保存从网页中提取的数据。为了做到这一点，我们在回调中使用`yield`关键字：

```
import scrapy


class ShowstartSpider(scrapy.Spider):
    name = "showstart"

    def start_requests(self):
        urls = [
            'https://www.showstart.com/',
        ]
        for url in urls:
            yield scrapy.Request(url=url, callback=self.parse)

    def parse(self, response):
        for show in response.css('a.activity-item'):
            yield {
                'href': show.css('a::attr(href)').get(),
                'name': show.css('div.name::text').get(),
                'price': show.css('p::text').get()
            }
```

如果你运行爬虫，它会在日志中输出提取的数据：

```
2021-06-13 20:08:16 [scrapy.core.scraper] DEBUG: Scraped from <200 https://www.showstart.com/>
{'href': '/event/91204', 'name': '#展览#北京奇葩减压馆·保龄球·射箭·分娩体验·蹦床·星空水床·团建·发泄·摔碗一站畅玩 ', 'price': '¥88'}
2021-06-13 20:08:16 [scrapy.core.scraper] DEBUG: Scraped from <200 https://www.showstart.com/>
{'href': '/event/133984', 'name': '2021北京国际光影艺术季  “万物共生-蔚蓝”户外光影艺术沉浸式体验展', 'price': '¥68'}
2021-06-13 20:08:16 [scrapy.core.scraper] DEBUG: Scraped from <200 https://www.showstart.com/>
{'href': '/event/152620', 'name': '【北喜脱口秀之夜】西直门-开心爆笑专场（单口+即兴+漫才）吐槽大会｜网红打卡胜地', 'price': '¥100'}
2021-06-13 20:08:16 [scrapy.core.scraper] DEBUG: Scraped from <200 https://www.showstart.com/>
{'href': '/event/152459', 'name': '【精品脱口秀节】爆笑段子专场｜北京喜剧中 心｜巨制盛宴X解压开心大会笑心果｜爆梗之夜', 'price': '¥100'}
2021-06-13 20:08:16 [scrapy.core.scraper] DEBUG: Scraped from <200 https://www.showstart.com/>
{'href': '/event/153038', 'name': '【周六喜剧之夜】北京脱口秀大会 『开心演出』单口吐槽 ×喜剧中心 高品质超大笑果-明星联盟！', 'price': '¥99'}
2021-06-13 20:08:16 [scrapy.core.scraper] DEBUG: Scraped from <200 https://www.showstart.com/>
{'href': '/event/153000', 'name': '【周中喜剧之夜】爆笑精品｜脱口秀大会X单口吐槽演出年会专场！', 'price': '¥99'}
2021-06-13 20:08:16 [scrapy.core.scraper] DEBUG: Scraped from <200 https://www.showstart.com/>
{'href': '/event/153030', 'name': '【精品脱口秀大会】解压爆梗之夜｜北京喜剧 中心巨制盛宴--吐槽现场（周中演出｜大剧场-超好笑X开心果演出）', 'price': '¥100'}
2021-06-13 20:08:16 [scrapy.core.scraper] DEBUG: Scraped from <200 https://www.showstart.com/>
{'href': '/event/153285', 'name': ' 三里屯 周六【爆笑脱口秀专场】袋鼠喜剧搞 笑零距离', 'price': '¥79'}
2021-06-13 20:08:16 [scrapy.core.scraper] DEBUG: Scraped from <200 https://www.showstart.com/>
{'href': '/event/153471', 'name': '【脱口秀专场】精品喜剧大会X北喜2021年巨制--解压狂欢｜东城爆笑专场', 'price': '¥100'}
2021-06-13 20:08:16 [scrapy.core.scraper] DEBUG: Scraped from <200 https://www.showstart.com/>
{'href': '/event/153472', 'name': '【脱口秀专场】精品喜剧大会X北喜2021年巨制--解压狂欢｜东城爆笑专场', 'price': '¥100'}
```

## 保存提取的数据

保存提取的数据最简单的方式是使用下面的命令：

`scrapy crawl showstart -O showstart.json`

这会生成一个保存了所有提取的数据以[JSON](https://www.json.org/json-en.html)序列化的`showstart.json`文件。

`-O`选项会覆盖已有的文件；使用`-o`则是在已有的文件中添加新的内容。但是，向JSON文件中添加新内容会让其违背JSON的格式，所以在已有文件中添加内容时应考虑使用其他的序列化格式，比如[JSON Lines](http://jsonlines.org/)：

`scrapy crawl showstart -o showstart.jl`

在更复杂的情形中，你也许需要把提取的数据保存的数据库中，可以参考[保存到数据库](https://maotouyingxia.github.io//scrapy//scrapy_tutorial_3)。

## 追踪链接

如果想要不仅仅是提取秀动网首页的演出信息，我们就需要追踪网页中的链接。

使用浏览器的检查工具我们可以发现 查看更多演出 的链接是用下面的HTML元素表示的

```
<div class="more-bar">
    <a href="/event/list" class="">查看更多演出<span class="m-icon icon-arrow"></span></a>
</div>
```

我们可以试着在shell中提取它：

```
>>> response.css('div.more-bar a').get()
'<a href="/event/list">查看更多演出<span class="m-icon icon-arrow"></span></a>'
```

这样可以得到anchor元素，但我们需要的是属性`href`。在Scrapy中，我们可以使用类似如下的CSS扩展来选择属性的内容：

```
>>> response.css('div.more-bar a::attr(href)').get()
'/event/list'
```

也可以使用`attrib`属性：

```
>>> response.css('div.more-bar a').attrib['href']
'/event/list'
```

把上面追踪链接的逻辑加入到爬虫中：

```
import scrapy


class ShowstartSpider(scrapy.Spider):
    name = "showstart"

    def start_requests(self):
        urls = [
            'https://www.showstart.com/',
        ]
        for url in urls:
            yield scrapy.Request(url=url, callback=self.parse)

    def parse(self, response):
        for show in response.css('a.activity-item'):
            yield {
                'href': show.css('a::attr(href)').get(),
                'name': show.css('div.name::text').get(),
                'price': show.css('p::text').get()
            }

        more_page = response.css('div.more-bar a::attr(href)').get()
        if more_page is not None:
            more_page = response.urljoin(more_page)
            yield scrapy.Request(more_page, callback=self.parse)
```

现在，在提取数据之后，`parse()`方法查找 查看更多演出 的链接，使用`urljoin()`方法拼接出一个完整的URL并发起一个新的请求，并把它自己注册为处理数据提取的回调方法。

这就是Scrapy追踪链接的机制：当你在回调方法中发起新的请求，Scrapy将安排发送这个请求并为其注册一个当请求结束时执行的回调方法。

使用这种方式，你可以编写按照你定义的规则追踪链接的复杂爬虫，并依据它访问的页面提取不同类型的数据。

- [Scrapy Tutorial — Scrapy 2.5.0 documentation](https://docs.scrapy.org/en/latest/intro/tutorial.html)