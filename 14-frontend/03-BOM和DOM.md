# BOM

## BOM 的顶级对象 window 以及常用操作方法

window 是浏览器的顶级对象，当调用 window 下的属性和方法时，可以省略 window。

#### 对话框

+ `alert()`：显示带有一段消息和一个确认按钮的警告框。
+ `prompt()`：显示可提示用户输入的对话框。
+ `confirm()`：显示带有一段消息以及确认按钮和取消按钮的对话框。

#### 页面加载事件

+ `onload`

```javascript
window.onload = function () {
  // 当页面加载完成执行
  // 当页面完全加载所有内容（包括图像、脚本文件、CSS 文件等）执行
};
```

+ `onunload`

```javascript
window.onunload = function () {
  // 当用户退出页面时执行
};
```

#### 浏览器尺寸

```javascript
var width = window.innerWidth;
document.documentElement.clientWidth;
document.body.clientWidth;

var height = window.innerHeight;
document.documentElement.clientHeight;
document.body.clientHeight;
```

上述代码可以获取所有浏览器的宽高（不包括工具栏/滚动条）。

#### 定时器

+ `setTimeout()` 方法在指定的毫秒数到达之后执行指定的函数，只执行一次。`clearTimeout()` 方法取消由 `setTimeout()` 方法设置的 `timeout`。

```javascript
// 创建一个定时器，2000毫秒后执行，返回定时器的标示
var timerId = setTimeout(function () {
  console.log("Hello shiyanlou");
}, 2000);

// 取消定时器的执行
clearTimeout(timerId);
```

+ `setInterval()` 方法设置定时调用的函数也就是可以按照给定的时间（单位毫秒）周期调用函数，`clearInterval()` 方法取消由 `setInterval()` 方法设置的 `timeout`。

```javascript
// 创建一个定时器，每隔 2 秒调用一次
var timerId = setInterval(function () {
  var date = new Date();
  console.log(date.toLocaleTimeString());
}, 2000);

// 取消定时器的执行
clearInterval(timerId);
```

注：BOM 的操作方法还有很多，但是一般情况下我们常用的就是上面所介绍的。有兴趣的可以自行百度了解 BOM 的更多操作方法和介绍。





# DOM

## 什么是 DOM？

DOM 是 W3C（万维网联盟）的标准。

DOM 定义了访问 HTML 和 XML 文档的标准：

> "W3C 文档对象模型 （DOM） 是中立于平台和语言的接口，它允许程序和脚本动态地访问和更新文档的内容、结构和样式。"

W3C DOM 标准被分为 3 个不同的部分：

- 核心 DOM - 针对任何结构化文档的标准模型
- XML DOM - 针对 XML 文档的标准模型
- HTML DOM - 针对 HTML 文档的标准模型

**编者注：**DOM 是 Document Object Model（文档对象模型）的缩写。

## HTML DOM 节点树

HTML DOM 将 HTML 文档视作树结构。这种结构被称为**节点树**：

HTML DOM 树实例

![DOM HTML tree](https://www.runoob.com/wp-content/uploads/2013/09/ct_htmltree.gif)

------

## 节点父、子和同胞

节点树中的节点彼此拥有层级关系。

我们常用**父（parent）**、**子（child）**和**同胞（sibling）**等术语来描述这些关系。父节点拥有子节点。同级的子节点被称为同胞（兄弟或姐妹）。

- 在节点树中，顶端节点被称为根（root）。
- 每个节点都有父节点、除了根（它没有父节点）。
- 一个节点可拥有任意数量的子节点。
- 同胞是拥有相同父节点的节点。

下面的图片展示了节点树的一部分，以及节点之间的关系：

![Node tree](https://www.runoob.com/wp-content/uploads/2013/09/dom_navigate.gif)

请看下面的 HTML 片段：

```html
<html>
  <head>
    <meta charset="utf-8">
    <title>DOM 教程</title>
  </head>
  <body>
    <h1>DOM 课程1</h1>
    <p>Hello world!</p>
  </body>
</html>
```

从上面的 HTML 中：

- \<html> 节点没有父节点；它是根节点

- \<head> 和 <body> 的父节点是 <html> 节点

- 文本节点 "Hello world!" 的父节点是 <p> 节点

并且：

- <html> 节点拥有两个子节点：<head> 和 <body>
- \<head> 节点拥有两个子节点：<meta> 与 <title> 节点

- \<title> 节点也拥有一个子节点：文本节点 "DOM 教程"

- \<h1> 和 <p> 节点是同胞节点，同时也是 <body> 的子节点

并且：

- \<head> 元素是 <html> 元素的首个子节点

- \<body> 元素是 <html> 元素的最后一个子节点

- \<h1> 元素是 <body> 元素的首个子节点

- <p> 元素是 <body> 元素的最后一个子节点

## DOM - 对象方法

 HTML DOM 常用方法：

| 方法                     | 描述                                                         |
| :----------------------- | :----------------------------------------------------------- |
| getElementById()         | 返回带有指定 ID 的元素。                                     |
| getElementsByTagName()   | 返回包含带有指定标签名称的所有元素的节点列表（集合/节点数组）。 |
| getElementsByClassName() | 返回包含带有指定类名的所有元素的节点列表。                   |
| appendChild()            | 把新的子节点添加到指定节点。                                 |
| removeChild()            | 删除子节点。                                                 |
| replaceChild()           | 替换子节点。                                                 |
| insertBefore()           | 在指定的子节点前面插入新的子节点。                           |
| createAttribute()        | 创建属性节点。                                               |
| createElement()          | 创建元素节点。                                               |
| createTextNode()         | 创建文本节点。                                               |
| getAttribute()           | 返回指定的属性值。                                           |
| setAttribute()           | 把指定属性设置或修改为指定的值。                             |

## DOM - 属性

| 属性       |                        |
| ---------- | ---------------------- |
| innerHTML  | 节点（元素）的文本值   |
| parentNode | 节点（元素）的父节点   |
| childNodes | 节点（元素）的子节点   |
| attributes | 节点（元素）的属性节点 |

### innerHTML 属性

获取元素内容的最简单方法是使用` innerHTML `属性。

innerHTML 属性对于`获取`或`替换` HTML 元素的`内容`很有用。

```html
<p id="intro">Hello World!</p>
 
<script>
var txt=document.getElementById("intro").innerHTML;
document.write(txt);
</script>
```

在上面的例子中，getElementById 是一个方法，而 innerHTML 是属性

>  innerHTML 属性可用于获取或改变任意 HTML 元素，包括 `<html>` 和 `<body>`

nodeName 属性规定节点的名称。

+ nodeName 是只读的
+ 元素节点的 nodeName 与标签名相同
+ 属性节点的 nodeName 与属性名相同
+ 文本节点的 nodeName 始终是 #text
+ 文档节点的 nodeName 始终是 #document

**注意：** nodeName 始终包含 HTML 元素的大写字母标签名。

### nodeType 属性

nodeType 属性返回节点的类型。nodeType 是只读的。

比较重要的节点类型有：

| 元素类型 | NodeType |
| :------- | :------- |
| 元素     | 1        |
| 属性     | 2        |
| 文本     | 3        |
| 注释     | 8        |
| 文档     | 9        |

#### 改变 HTML 属性

语法：

```javascript
document.getElementById(id).attribute = new value();
```

例子：

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8" />
    <title></title>
  </head>
  <body>
    <img id="image" src="https://www.baidu.com/img/bd_logo1.png" />
    <script>
      document.getElementById("image").src =
        "https://static.shiyanlou.com/img/shiyanlou_logo.svg";
    </script>
  </body>
</html>
```

## DOM - 访问

### 访问 HTML 元素（节点）

访问 HTML 元素等同于访问节点

您能够以不同的方式来访问 HTML 元素：

| 方法                     | 实例                                                         |
| ------------------------ | ------------------------------------------------------------ |
| getElementById()         | 下面的例子获取 id="intro" 的元素：<br />`document.getElementById("intro");` |
| getElementsByTagName()   | 下面的例子返回包含文档中所有 <p> 元素的列表：<br />`document.getElementsByTagName("p");` |
| getElementsByClassName() | 返回包含 class="intro" 的所有元素的一个列表：<br />`document.getElementsByClassName("intro");` |

## DOM - 修改

> 修改 HTML = 改变元素、属性、样式和事件

### 修改 HTML 元素

修改 HTML DOM 意味着许多不同的方面：

+ 改变 HTML 内容
+ 改变 CSS 样式
+ 改变 HTML 属性
+ 创建新的 HTML 元素
+ 删除已有的 HTML 元素
+ 改变事件（处理程序）

在接下来的章节，我们会深入学习修改 HTML DOM 的常用方法。

### 创建 HTML 内容

改变元素内容的最简单的方法是使用 innerHTML 属性。

下面的例子改变一个 <p> 元素的 HTML 内容：

```html
<p id="p1">Hello World!</p>   
<script> 
    document.getElementById("p1").innerHTML="新文本!"; 
</script>   
<p>段落通过脚本来修改内容。</p>
```

### 改变 HTML 样式

通过 HTML DOM，您能够访问 HTML 元素的样式对象。

下面的例子改变一个段落的 HTML 样式：

```html
<p id="p1">Hello world!</p>
<p id="p2">Hello world!</p>
 
<script>
document.getElementById("p2").style.color="blue";
document.getElementById("p2").style.fontFamily="Arial";
document.getElementById("p2").style.fontSize="larger";
</script>
```

### 使用事件

HTML DOM 允许您在事件发生时执行代码。

当 HTML 元素"有事情发生"时，浏览器就会生成事件：

+ 在元素上点击
+ 加载页面
+ 改变输入字段

你可以在下一章学习更多有关事件的内容。

下面两个例子在按钮被点击时改变 <body> 元素的背景色：

```html
<input type="button"
onclick="document.body.style.backgroundColor='lavender';"
value="修改背景颜色">
```

#### 事件三要素

事件由：事件源 + 事件类型 + 事件处理程序组成。

+ 事件源：触发事件的元素。
+ 事件类型：事件的触发方式（比如鼠标点击或键盘点击）。
+ 事件处理程序：事件触发后要执行的代码（函数形式，匿名函数）。

#### 常用的事件

| 事件名      | 说明                                 |
| ----------- | ------------------------------------ |
| onclick     | 鼠标单击                             |
| ondblclick  | 鼠标双击                             |
| onkeyup     | 按下并释放键盘上的一个键时触发       |
| onchange    | 文本内容或下拉菜单中的选项发生改变   |
| onfocus     | 获得焦点，表示文本框等获得鼠标光标。 |
| onblur      | 失去焦点，表示文本框等失去鼠标光标。 |
| onmouseover | 鼠标悬停，即鼠标停留在图片等的上方   |
| onmouseout  | 鼠标移出，即离开图片等所在的区域     |
| onload      | 网页文档加载事件                     |
| onunload    | 关闭网页时                           |
| onsubmit    | 表单提交事件                         |
| onreset     | 重置表单时                           |

### 获取网页所有链接地址

```js
for (var i=0;i<document.getElementsByTagName("a").length;i++){
    console.log(document.getElementsByTagName("a")[i].href)
}
```

