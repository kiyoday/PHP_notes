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

## HTML DOM 树实例

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

## DOM 对象方法

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

## DOM 属性

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