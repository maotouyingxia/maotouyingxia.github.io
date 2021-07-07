# 列表、表格与表单

## （一）列表

HTML支持有序、无序和自定义列表。和其他HTML元素一样，列表支持嵌套。

无序列表由`<ul>`标签(unordered list)定义，每个列表项由`<li>`标签(list item)定义，并在页面上使用粗体圆点进行标记。可以使用`style`属性的`list-style-type`来指定用于标记的符号，如`disc`为圆点，`circle`为圆圈，`square`为正方形。

```
<ul>
    <li>ck2</li>
    <li>hoi4</li>
    <li>v2</li>
    <li>eu4</li>
</ul>
```

有序列表由`<ol>`标签(ordered list)定义，每个列表项由`<li>`标签(list item)定义，并在页面上使用数字进行标记。可以用`type`属性指定用于标记的序号，如`A`为大写字母，`a`为小写字母，`I`为大写罗马数字，`i`为小写罗马数字。

```
<ol>
    <li>ck2</li>
    <li>hoi4</li>
    <li>v2</li>
    <li>eu4</li>
</ol>
```

自定义列表由`<dl>`标签(definition list)定义，每个自定义列表项由`<dt>`标签(definition term)定义，每个自定义列表项的定义由`<dd>`标签(definition description)定义。

```
<dl>
    <dt>ck2</dt>
    <dd>crusader kings 2</dd>
    <dt>hoi4</dt>
    <dd>hearts of iron 4</dd>
    <dt>v2</dt>
    <dd>victoria 2</dd>
    <dt>eu4</dt>
    <dd>europa universalis 4</dd>
</dl>
```

## （二）表格

HTML表格由`<table>`标签定义。属性`border`定义表格的边框，如果不定义定义，表格将不显示边框。属性`cellpadding`定义单元格边距。属性`cellspacing`定义单元格间距。表格的标题由`<caption>`标题定义。表格的表头由`<th>`标签(table header cell)定义。属性`rowspan`可以指定表头的范围。表格的行由`<tr>`标签(table row)定义。每行的单元格由`<td>`标签(table data cell)定义。

```
<table>
    <caption>dragon and dungeon</caption>
    <tr>
        <th></th>
        <th>lawful</th>
        <th>neutral</th>
        <th>chaotic</th>
    </tr>
    <tr>
        <th>good</th>
        <td>lawful good</td>
        <td>neutral good</td>
        <td>chaotic good</td>
    </tr>
    <tr>
        <th>neutral</th>
        <td>lawful neutral</td>
        <td>neutral</td>
        <td>chaotic neutral</td>
    </tr>
    <tr>
        <th>evil</th>
        <td>lawful evil</td>
        <td>neutral evil</td>
        <td>chaotic evil</td>
    </tr>
</table>
```

## （三）表单

表单元素允许用户在表单中输入内容，如文本，下拉列表，单选框，复选框。表单元素由`<form>`标签定义。大多数情况下使用到的表单标签是`<input>`输入标签，其中type属性用于定义输入的类型。表单的`action`属性用于指定向何处提交表单。

### （1）文本

text用于输入文本

```
<form>
    First name: <input type="text" name="firstname"><br />
    Last name: <input type="text" name="lastname">
</form>
```

### （2）密码

password用于输入密码

```
<form>
    Password: <input type="password" name="pwd">
</form>
``` 

### （3）单选框

radio用于表示单选框

```
<form>
    <input type="radio" name="sex" value="male">Male<br />
    <input type="radio" name="sex" value="female">Female
</form>
```

### （4）复选框

checkbox用于表示复选框

```
<form>
    <input type="checkbox" name="candidate" value="Hillary">Crooked Hillary<br />
    <input type="checkbox" name="candidate" value="Joe">Creepy Joe
</form>
```

### （5）按钮

button用于表示按钮

```
<form action="">
    <input type="button" value="Nuclear weapon">
</form>
```

submit用于提交按钮

```
<form name="input" action="vote.jsp" method="get">
    Candidate: <input type="text" name="vote">
    <input type="submit" value="Submit">
</form>
```

### （6）下拉列表

可以用`<select>`标签和`<option>`标签创建下拉列表，其中`selected`属性用于指定预选值。

```
<form action="cars.jsp">
    <select name="cars">
        <option value="volvo">Volvo</option>
        <option value="saab" selected>Saab</option>
        <option value="fiat">Fiat</option>
        <option value="audi">Audi</option>
    </select>
</form>
```

### （7）表单边框

可以用`<fieldset>`标签和`<legend>`标签创建带边框的表单

```
<form action="login.jsp">
    <fieldset>
        <legend>Who are you?</legend>
        Name: <input type="text"><br />
        Email: <input type="text"><br />
    </fieldset>
</form>
```

- [HTML 表格 入门 - 学习 Web 开发](https://developer.mozilla.org/zh-CN/docs/Learn/HTML/Tables/Basics)
- [创建我的第一个表单 - 学习 Web 开发](https://developer.mozilla.org/zh-CN/docs/Learn/Forms/Your_first_form)