# 文本格式化与样式

## （一）文本格式化

### （1）粗体

使用标签`<b>`(bold)或标签`<strong>`输出粗体

```
<b>bold</b>
<br />
<strong>strong</strong>
```

### （2）斜体

使用标签`<i>`(Italic)或标签`<em>`(emphasize)输出斜体

```
<i>italic</i>
<br />
<em>emphasize</em>
```
### （3）上下标

使用标签`<sup>`(superscript)输出上标，标签`<sub>`(subscript)输出下标

```
<sup>superscript</sup>
<br />
<sub>subscript</sub>
```

### （4）缩写

标签`<abbr>`(abbreviation)用于表示缩写，在鼠标移至缩略词上方时，浏览器会展示title属性定义的完整文本。

`<abbr title="World Wide Web">WWW</abbr>`

### （5）文本方向

标签`<bdo>`(bi-direction override)用于定义文本的方向，其中dir属性(direction)用于定义具体的文本方向。

`<p><bdo dir="rtl">之乎者也</bdo></p>`

### （6）引用

标签`<q>`(quotation)用于表示引用。

`<p>Athletics:<q>Who you are is not enough</q></p>`

### （7）特殊效果

标签`<del>`(deleted)用于删除字效果，标签`<ins>`(inserted)用于插入字效果。

`<p>I am <del>fine</del> <ins>unhappy</ins></p>`

### （8）计算机相关

某些标签用于计算机相关的文本格式化，如标签`<code>`表示计算机输出，标签`<kbd>`(keyboard)表示键盘输入，标签`<samp>`(sample)表示计算机代码样本，标签`<var>`(variable)表示计算机变量。

```
<code>computer output</code>
<br />
<kbd>keyboard input</kbd>
<br />
<samp>computer code sample</samp>
<br />
<var>computer variable</var>
```

### （9）作者相关

标签`<address>`用于表示文档作者的联系信息。

```
<address>
Written by <a href="mailto:BilboBaggins@mail.com">Bilbo Baggins</a>.<br />
Visit me at:<br />
BilboBaggins.com<br />
The Shire<br />
</address>
```

## （二）样式

### （1）内联样式

在个别元素需要特殊的样式时，可以在相应的标签中使用style属性。其中`color`用于指定颜色，`background-color`用于指定背景颜色，`font-family`用于指定字体，`font-size`用于指定字体大小，`text-align`用于指定对齐方式。

```
<p style="color: aqua;margin-left: 20px;">paragraph</p>

<p style="background-color: darkturquoise;font-family: Verdana, Geneva, Tahoma, sans-serif;">another paragraph</p>

<p style="font-size: 20px;text-align: center;">another paragraph again</p>
```

### （2）内联样式表

在单个页面需要特殊样式时，可以在相应的页面头部标签`<head>`使用`<style>`标签。

```
<style type="text/css">
    body {background-color: grey;}
    h1 {color: cornflowerblue;}
    p {color: white;}
</style>
```

### （3）外部样式

在同一个样式需要被应用到多个页面时，可以在相应的页面头部`<head>`使用`<link>`标签链接到外部的CSS样式文件。

```
<link rel="stylesheet" type="text/css" href="style.css">
```

- [HTML 文字处理基础 - 学习 Web 开发](https://developer.mozilla.org/zh-CN/docs/learn/HTML/Introduction_to_HTML/HTML_text_fundamentals)
- [高阶文字排版 - 学习 Web 开发](https://developer.mozilla.org/zh-CN/docs/Learn/HTML/Introduction_to_HTML/Advanced_text_formatting)