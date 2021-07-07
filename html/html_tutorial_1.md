# 基本概念

## （一）标签

HTML标签(tag)是用尖括号包围起来的关键字，如`<html>`。它们通常成对出现，如`<head>`和`</head>`。称前者为开始标签(openning tag)，后者为结束标签(closing tag)。HTML标签用于定义HTML元素。

## （二）元素

HTML元素(element)是开始标签与结束标签之间的内容，如`<p>My Paragraph</p>`。HTML元素支持嵌套，网页就是典型的HTML元素嵌套。

有些HTML元素不包含任何内容，称它们为空元素。因为不包含内容，所以空元素只有开始标签，并且需要在开始标签中关闭，如用于换行的`<br />`标签(break)和表示分割线的`<hr />`标签(horizontal rule)。

## （三）属性

HTML属性(attribute)用于提供HTML元素的附加信息。属性总是在开始标签中以键值对的形式出现。如`href`是链接`<a>`的一个属性，其定义了链接指向的地址。

`<a href="https://www.mysite.com">my site</a>`

## （四）元素

大多数HTML元素被定义为块级元素(block)或内联元素(inline)。块级元素在显示时通常会以新行开始，如`<h1>`, `<p>`, `<ul>`, `<table>`。内联元素在显示时通常不会以新行开始，如`<b>`, `<td>`, `<a>`, `<img>`。

`<div>`元素(division)是块级元素，是可用于组合其他HTML元素的容器。与CSS一起使用可以对大的内容块设置样式属性。`<div>`元素还可用于创建页面的布局。

`<span>`元素是内联元素，是可用于文本的容器，与CSS一起使用可以对部分文本设置样式属性。

## （五）注释

HTML注释(comment)用于提高代码的可读性。注释会被浏览器忽略，即不在页面上显示。

`<!-- this is a comment -->`

## （六）字符实体

HTML字符实体用于显示HTML中的预留字符，如<需用`&lt`，`&#60`或`&#060`表示。因为浏览器会忽略HTML代码中多余的空格，所以可以使用`&nbsp`，即(non-breaking space)来显示多余的空格。

- [开始学习 HTML - 学习 Web 开发](https://developer.mozilla.org/zh-CN/docs/Learn/HTML/Introduction_to_HTML/Getting_started)