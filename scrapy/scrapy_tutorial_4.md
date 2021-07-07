# 提取动态加载的内容

有些网页需要额外的请求来获取数据，这时候我们就需要复现那些获取需要的数据的请求。有时候重现特定的请求比较困难，或者你想得到一些请求给不了你的东西，比如网页在浏览器中的截图。在这个教程中，我们将使用 [scrapy-splash](https://github.com/scrapy-plugins/scrapy-splash) 来集成 [Splash](https://github.com/scrapinghub/splash) 的JavaScript渲染服务。

## 安装

使用pip安装scrapy-splash：

`pip install scrapy-splash`

scrapy-splash会调用Splash的HTTP API，所以你需要一个Splash实例。你可以使用下面的命令通过 [Docker](https://www.docker.com/) 运行Splash服务：

`docker run -p 8050:8050 scrapinghub/splash`

这条命令会拉取splash的镜像并在8050端口上运行Splash服务。Docker是广受欢迎的容器技术，有关Docker的内容可以参考 [Docker](https://maotouyingxia.github.io//#docker)。

## 配置

在你的Scrapy项目的`settings.py`文件里添加Splash服务的地址：

`SPLASH_URL = 'http://localhost:8050'`

把Splash中间件添加到`settings.py`文件的`DOWNLOADER_MIDDLEWARES`中并改变HttpCompressionMiddleware的优先级：

```
DOWNLOADER_MIDDLEWARES = {
    'scrapy_splash.SplashCookiesMiddleware': 723,
    'scrapy_splash.SplashMiddleware': 725,
    'scrapy.downloadermiddlewares.httpcompression.HttpCompressionMiddleware': 810,
}
```

在默认的scrapy设置中优先级723在HttpProxyMiddleware (750)的前面。改变HttpCompressionMiddleware的优先级是为了进行高级响应处理，具体可以参考 [issues/1895](https://github.com/scrapy/scrapy/issues/1895)

把`SplashDeduplicateArgsMiddleware`添加到`settings.py`文件的`SPIDER_MIDDLEWARES`中：

```
SPIDER_MIDDLEWARES = {
    'scrapy_splash.SplashDeduplicateArgsMiddleware': 100,
}
```

这个中间件是用来支持`cache_args`特性的，它通过不多次在磁盘请求队列中存储重复的Splash参数来节省磁盘空间。

设定自定义的`DUPEFILTER_CLASS`：

`DUPEFILTER_CLASS = 'scrapy_splash.SplashAwareDupeFilter'`

如果你使用Scrapy HTTP缓存，那么还需要一个自定义的缓存存储后端。scrapy-splash提供了一个`scrapy.contrib.httpcache.FilesystemCacheStorage`的子类：

`HTTPCACHE_STORAGE = 'scrapy_splash.SplashAwareFSCacheStorage'`

## 使用

通过`scrapy_splash.SplashRequest`即可使用Splash渲染请求：

```
from scrapy_splash import SplashRequest

...

    def start_requests(self):
        for url in self.start_urls:
            yield SplashRequest(url, self.parse, args={'wait': 0.5})
```

在稍为复杂的情形中，你可能需要编写[Lua](http://www.lua.org/)脚本：

```
def start_requests(self):
    script = """
        function main(splash)
            splash:go(splash.args.url)
            splash:wait(1)
            local offset
            local scroll_to = splash:jsfunc("window.scrollTo")
            for i = 1,10 do
                offset = splash:evaljs("document.documentElement.scrollHeight")
                scroll_to(0, offset)
                splash:wait(1)
            end
            return splash:html()
        end
    """
    for url in self.start_urls:
        yield SplashRequest(url, self.parse, endpoint='execute', args={'lua_source': script})
```

这里的脚本执行了10次向下滚动页面的操作，使用脚本需要在`SplashRequest`的`args`中指定脚本源。

Splash服务在`localhost:8050`提供了一个简单的界面，建议预先在这里编写你的Lua脚本并验证效果。你可以在Lua脚本中执行JavaScript代码，比如上面的脚本使用`splash:jsfunc`获取JavaScript函数，使用`splash:evaljs`得到网页的某些属性。

使用`splash:wait`是因为每次操作后需要一定时间来相应地渲染网页。

`splash:html()`表示获取渲染完成后网页的HTML文本，你还可以使用`splash:png()`来获取渲染完成后的网页截图。

- [scrapy-plugins/scrapy-splash: Scrapy+Splash for JavaScript integration](https://github.com/scrapy-plugins/scrapy-splash)
- [Splash Scripts Tutorial — Splash 3.5 documentation](https://splash.readthedocs.io/en/latest/scripting-tutorial.html)