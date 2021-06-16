# HTML元素

## （一）头部元素

HTML的头部元素是标签`<head>`内的元素。

### （1）标题

标签`<title>`定义文档的标题，文档的标题会显示在浏览器的标签页上，浏览器的收藏夹中，以及搜索引擎的结果页面。

`<title>My Page</title>`

### （2）链接

标签`<link>`定义文档与外部资源之间的关系，通常用于链接到外部样式表，即CSS文件。其中rel属性(reload)定义当前文档与链接文档的关系，type属性定义链接文档的MIME(Multipurpose Internet Mail Extension)类型，href属性(hypertext reference)定义链接文档的位置。

`<link rel="stylesheet" type="text/css" href="style.css">`

### （3）行内样式表

标签`<style>`定义HTML文档的行内样式表。

`<style type="text/css"> body {background-color: grey;} p {color: wheat;} </style>`

### （4）元数据

标签`<meta>`定义网页的元数据。元数据不会在浏览器中显示，但会被浏览器解析。元数据一般据包含网页描述、关键词、最后修改时间、作者等等。其中属性name指定元数据的名称，相应的content属性指定元数据的内容；属性http-equiv相当于HTTP的头文件，用于向浏览器传递信息，相应的content属性指定传递的变量。

```
<meta name="keywords" content="HTML, tutorial, sample">
<meta name="description" content="this is beginner's HTML tutorial">
<meta name="author" content="maotouyingxia">
<meta http-equiv="refresh" content="30">
```

## （二）主体元素

HTML的主体元素是标签`<body>`内的元素。

### （1）标题

HTML标题（heading）是通过`<h1>`-`<h6>`标签来定义的。标题的重要性(大小)从`<h1>`到`<h6>`依次递减。标题的内容很重要，因为标题是搜索引擎为网页的结构和内容编制索引的依据。

```
<h1>maximum heading</h1>
<h2>biggest heading</h2>
<h3>bigger heading</h3>
<h4>smaller heading</h4>
<h5>smallest heading</h5>
<h6>minimum heading</h6>
```

### （2）段落

HTML段落（paragraph）是通过标签`<p>`来定义的。

```
<p>paragraph</p>
<p>another paragraph</p>
```

因为浏览器在显示页面时会忽略多余的空格，所以在源代码中使用空格和换行不能使页面有相应的改变。正确的换行方式是使用`<br>`标签。如果有需要，也可以使用`<pre>`标签(preformatted)保留源代码中的空格和换行。

```
<p>this paragraph<br />shows how to<br />wrap properly</p>

<p><pre>text here    remain its
             origin format</pre></p>
```

### （3）链接

HTML链接（anchor）是通过标签`<a>`来定义的。其中href属性(hypertext reference)用于指定链接指向的地址。target属性用于文档重定向，"_blank"表示在空白页打开链接，"_top"表示回到页面顶部。

```
<a href="https://www.mysite.com" target="_blank">my site</a>

<a href="https://www.mysite.com" target="_top">top</a>
```

可以通过链接跳转到当前文档的某一部分。这需要使用id属性，即首先为跳转到的地方设置好id，然后创建一个指向此处的链接。

```
<a id="reference" href="https://developer.mozilla.org/zh-CN/docs/Learn/HTML" target="_blank">reference</a>

<a href="#reference">go to reference</a>
```

### （4）图像

HTML图像（image）是通过标签`<img>`来定义的。标签`<img>`是空标签。其中src属性用于指定图像名称，width属性和height属性用于指定图像的宽度和高度，alt属性(alter)用于指定一段在图片加载失败时显示的文本。

```
<img src="picture.jpg" width="618" height="1000" alt="load failed"/>
```

### （5）框架

HTML框架可以用来显示另一个页面。它由标签`<iframe>`(inline frame)定义。其中属性src指定显示的页面，属性width和属性height指定页面的大小，属性frameborder指定边框的大小。        

```
<iframe src="page.html" width="200" height="200" frameborder="0"></iframe>
```

可以和标签`<a>`一起使用以显示某个链接指向的页面。

```
<iframe name="iframe_a"></iframe>
<p><a href="https://www.mysite.com" target="iframe_a">my site</a></p>
```

### （6）脚本

HTML的脚本由`<script>`标签定义，可以在`<script>`标签中编写JavaScript脚本，也可以使用src属性指向外部的脚本文件。

```
<script>
	document.write("Hey Jude");
</script>
```

`<noscript>`标签可以在浏览器不支持脚本时显示替代的内容。

```
<noscript>
	Sorry, your browser doesn't support JavaScript
</noscript>
```

- [<head>标签里有什么? Metadata-HTML中的元数据 - 学习 Web 开发 | MDN](https://developer.mozilla.org/zh-CN/docs/Learn/HTML/Introduction_to_HTML/The_head_metadata_in_HTML)
- [HTML中的图片 - 学习 Web 开发 | MDN](https://developer.mozilla.org/zh-CN/docs/Learn/HTML/Multimedia_and_embedding/Images_in_HTML)
- [建立超链接 - 学习 Web 开发 | MDN](https://developer.mozilla.org/zh-CN/docs/Learn/HTML/Introduction_to_HTML/Creating_hyperlinks)