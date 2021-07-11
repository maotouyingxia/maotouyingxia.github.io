# 提取动态加载的内容

当你在浏览器中加载某些网页时，它们会显示你想要的数据。然而，当你用Scrapy下载它们时，你却无法使用 [选择器](https://docs.scrapy.org/en/latest/topics/selectors.html#topics-selectors) 来获取这些数据。

当这种情况发生时，推荐的方法是 [查找数据源](#查找数据源) 并从它们提取数据。

如果你不能做到这一点，你还是能够通过你的浏览器中的 [DOM](https://docs.scrapy.org/en/latest/topics/developer-tools.html#topics-livedom) 访问到需要的数据，参见 [预渲染JavaScript](#预渲染javascript)。

## 查找数据源

要提取需要的数据，你必须找出它的来源。

如果数据不是基于文本的格式，比如图像或PDF文档，使用你的浏览器的 [网络工具](https://docs.scrapy.org/en/latest/topics/developer-tools.html#topics-network-tool) 找到对应的请求，然后 [重现它](#重现请求)。

如果你的浏览器能让你以文本的形式选择需要的数据，那么它也许被定义在嵌入的JavaScript代码中，或者以基于文本的格式从外部源加载。

在这种情况下，你可以使用类似 [wgrep](https://github.com/stav/wgrep) 的工具找出外部源的URL。

如果数据来自于初始的URL本身，你需要 [查看网页源代码](#查看网页源代码) 来确定数据的位置。

如果数据来自于一个不同的URL，你需要[重现对应的请求](#重现请求) 。

## 查看网页源代码

有时候你需要查看网页的源代码（不是 [DOM](https://docs.scrapy.org/en/latest/topics/developer-tools.html#topics-livedom) ）来确定需要数据的位置。

使用Scrapy的`fetch`指令下载Scrapy见到的的网页内容：

`scrapy fetch --nolog https://example.com > response.html`

如果需要的数据在嵌入的`<script/>`元素的JavaScript代码中，参见 [分析JavaScript代码](#分析javascript代码)

如果你找不到需要的数据，你首先需要确定不仅仅是因为Scrapy：使用类似 [curl](https://curl.haxx.se/) 或 [wget](https://www.gnu.org/software/wget/) 的HTTP客户端下载网页并查看能否在它们得到的响应中找到需要的数据。

如果它们得到的响应中存在需要的数据，修改你的Scrapy中的`Request`以与这些HTTP客户端一致。例如，尝试使用相同的user-agent字符串（`USER_AGENT`）或者相同的`headers`。

如果它们得到的响应没有需要的数据，你需要让你的请求更像由一个浏览器发出，参见 [重现请求](#重现请求) 。

## 重现请求

有时候我们需要以类似浏览器的方式重现一个请求。

使用你的浏览器的 [网络工具](https://docs.scrapy.org/en/latest/topics/developer-tools.html#topics-network-tool) 查看你的浏览器如何发出需要的请求，然后尝试用Scrapy重现这个请求。

产生一个具有相同Http方法和URL的`Request`也许是足够的。然而，你可能需要重现这个请求的请求体，请求头以及表格参数。

因为所有的主流浏览器都支持以 [cURL](https://curl.haxx.se/) 的形式导出请求，所以Scrapy提供了一个名为`from_curl()`的方法以从cURL命令生成对应的`Request`。有关这方面的更多信息请参见网络工具部分中的 [request from curl](https://docs.scrapy.org/en/latest/topics/developer-tools.html#requests-from-curl)。

一旦你得到了需要的响应，你可以从它 [提取数据](#处理不同的响应格式)

你可以使用Scrapy重现任何请求。然而，有时重现所有的请求在开发中并不是很高效。如果你遇到了这种情况，并且爬取速度不是你首要关注的事，你可以考虑使用 [JavaScript预渲染](#预渲染javascript) 。

如果你有时能得到需要的响应，但并不是每次都能，那么问题可能并不在于你的请求，而是你请求的服务器。你请求的服务器可能有问题，负载过重，或者 [禁止](https://docs.scrapy.org/en/latest/topics/practices.html#bans) 了你的部分请求。

注意到要把cURL命令转换为Scrapy请求，你可以使用 [curl2scrapy](https://michael-shub.github.io/curl2scrapy/)

## 处理不同的响应格式

一旦你得到了需要数据的响应，你需要根据响应的格式提取需要的数据：
- 如果响应是HTML或XML，像通常一样使用 [selectors](https://docs.scrapy.org/en/latest/topics/selectors.html#topics-selectors)
- 如果响应是JSON，使用`json.loads()`从`response.text`中加载需要的数据：
  `data = json.loads(response.text)`
  如果需要的数据是嵌入在JSON中的HTML或XML代码，你可以将它们加载到`selector`中并像通常一样使用它：
  `selector = Selector(data['html'])`
- 如果响应是JavaScipt，或者包含有需要数据的`<script>`元素的HTML，参见 [分析JavaScript代码](#分析javascript代码) 。
- 如果响应是CSS，使用 [正则表达式](https://docs.python.org/3/library/re.html)从`response.text`中提取需要的数据。
- 如果响应是图像或者其他基于图像的格式（例如PDF）,从`response.body`中以字节的形式读取响应并使用OCR以文本的方式提取需要的数据。
  例如，你可以使用 [pytesseract](https://github.com/madmaze/pytesseract) 。要从PDF中读取表格，[tabula-py](https://github.com/chezou/tabula-py) 也许是更好的选择。
- 如果响应是SVG，或者包含有需要数据的嵌入SVG的HTML，你可以使用 [selectors](https://docs.scrapy.org/en/latest/topics/selectors.html#topics-selectors) 提取需要的数据，因为SVG是基于XML的。
  其他情形下，你也许需要把SVG代码转换成栅格图像，然后处理栅格图像

## 分析JavaScript代码

如果需要的数据被硬编码在JavaScript中，你需要首先获取JavaScript代码：
- 如果JavaScript代码在一个JavaScript文件中，只需要从`response.text`读取即可
- 如果JavaScript代码在HTML页面的`<script/>`元素中，使用 [selectors](https://docs.scrapy.org/en/latest/topics/selectors.html#topics-selectors) 从`<script/>`元素中提取文本。

一旦你获得了JavaScript代码的文本，你可以从它获取需要的数据：
- 你可以使用 [正则表达式](https://docs.python.org/3/library/re.html) 以JSON的格式提取需要的数据，然后使用`json.loads()`分析。
  例如，如果JavaScript代码包含像`var data = {"field": "value"};`的行分隔符，你可以像下面这样提取数据：
  ```
  >>> pattern = r'\bvar\s+data\s*=\s*(\{.*?\})\s*;\s*\n'
  >>> json_data = response.css('script::text').re_first(pattern)
  >>> json.loads(json_data)
  {'field': 'value'}
  ```
- [chompjs](https://github.com/Nykakin/chompjs) 提供了一个把JavaScript对象转换成`dict`的接口。
  例如，如果JavaScript包含类似`var data = {field: "value", secondField: "second value"};`的内容。你可以像下面这样提取数据：

  ```
  >>> import chompjs
  >>> javascript = response.css('script::text').get()
  >>> data = chompjs.parse_js_object(javascript)
  >>> data
  {'field': 'value', 'secondField': 'second value'}
  ```

- 除此以外，可以使用使用 [js2xml](https://github.com/scrapinghub/js2xml) 把JavaScript代码转换成XML文档，然后用 [selectors](https://docs.scrapy.org/en/latest/topics/selectors.html#topics-selectors) 进行分析。
  例如，如果JavaScript代码包含类似`var data = {field: "value"};`的内容，你可以像下面这样提取数据：

  ```
  >>> import js2xml
  >>> import lxml.etree
  >>> from parsel import Selector
  >>> javascript = response.css('script::text').get()
  >>> xml = lxml.etree.tostring(js2xml.parse(javascript), encoding='unicode')
  >>> selector = Selector(text=xml)
  >>> selector.css('var[name="data"]').get()
  '<var name="data"><object><property name="field"><string>value</string></property></object></var>'
  ```

## 预渲染JavaScript

对于从额外的请求获取数据的网页，重现这些包含需要的数据的请求是推荐的方式。这种努力通常是值得的：通过最少的分析和网络传输得到结构化的，完整的数据。

然而，有时要重现特定的请求会特别困难。或者你可能需要一些请求不能给你的东西，例如在浏览器中见到的网页截屏。

在这些情形中可以使用 [Splash](https://github.com/scrapinghub/splash) JavaScript预渲染服务，并使用 [scrapy-splash](https://github.com/scrapy-plugins/scrapy-splash) 进行整合。

Splash以HTML的形式返回网页的 [DOM](https://docs.scrapy.org/en/latest/topics/developer-tools.html#topics-livedom), 因此，你可以使用 [selectors](https://docs.scrapy.org/en/latest/topics/selectors.html#topics-selectors) 进行分析。它通过 [配置](https://splash.readthedocs.io/en/stable/api.html) 或 [脚本](https://splash.readthedocs.io/en/stable/scripting-tutorial.html) 提供了极大的灵活性。

如果你需要一些Splash提供不了的东西，比如使用Python代码实时地与DOM交互而不是使用预先写好的脚本，或者处理多个浏览器窗口，你也许需要 [使用无头浏览器](#使用无头浏览器) 。

## 使用无头浏览器

[无头浏览器](https://en.wikipedia.org/wiki/Headless_browser) 是提供用于自动化的接口的特殊浏览器。

在Scrapy中使用无头浏览器最简单的方式是使用 [Selenium](https://www.selenium.dev/)，并使用 [scrapy-selenium](https://github.com/clemfromspace/scrapy-selenium) 进行整合。

[Selecting dynamically-loaded content — Scrapy 2.5.0 documentation](https://docs.scrapy.org/en/latest/topics/dynamic-content.html)