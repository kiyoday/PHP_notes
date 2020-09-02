## jQuery简介

### 什么是 jQuery

jQuery 是开源软件，使用 MIT 许可证授权。jQuery 的语法设计使得许多操作变得容易，如`操作文档对象（document）`、`选择文档对象模型（DOM）元素`、创建动画效果、处理事件、以及开发 Ajax 程序。jQuery 也给开发人员提供了在其上创建插件的能力。这使开发人员可以对底层交互与动画、高级效果和高级主题化的组件进行抽象化。模块化的方式使 jQuery 函数库能够创建功能强大的动态网页以及网络应用程序。

微软和诺基亚已宣布在他们的平台上绑定 jQuery。微软最初在 Visual Studio 中集成了 jQuery 以便在微软自己的 ASP.NET AJAX 框架和 ASP.NET MVC Framework 中使用，而诺基亚则在他的 Web 运行时组件开发平台中集成了 jQuery。MediaWiki 自从 1.16 版本后也开始使用 jQuery。

jQuery 1.3 版以后，引入全新的层叠样式表（CSS）选择器引擎 Sizzle。同时不再提供 Packed 版本，因为解压缩所消耗的时间，远大于所节省的下载时间，且不利于调试，且已有 Google AJAX Libraries API 等公开站台提供 jQuery 的 js 的引用服务，故 Packed 版本原本的优点已荡然无存。

注：定义来自维基百科。

我们可以简单的理解为 jQuery 是一个 JavaScript 函数库。jQuery 是一个轻量级的"写的少，做的多"的 JavaScript 库。

### 特色

+ 使用多浏览器开源选择器引擎 Sizzle（jQuery 项目的派生产品）进行 DOM 元素选择
+ 基于 CSS 选择器的 DOM 操作，使用元素的名称和属性（如 id 和 class）作为选择 DOM 中节点的条件
+ 事件
+ 特效和动画
+ Ajax
+ Deferred 和 Promise 对象来控制异步处理
+ JSON 解析
+ 通过插件扩展
+ 工具函数，如特征检测
+ 现代浏览器中本地的兼容性方法，但对于旧版浏览器需要后备（fallback）方法，比如 inArray()和 each()
+ 多浏览器（不要与跨浏览器混淆）支持

## jQuery 语法

jQuery 语法是为 HTML 元素的选取编制的，可以对元素执行某些操作。

基础语法是：

```js
$(selector).action();
```

+ 美元符号` $ `定义 jQuery。
+ 选择符（selector）“查询”和“查找” HTML 元素。
+ jQuery 的 action() 执行对元素的操作。

另外需要注意的是：在 jQuery 库中`$` 符号就是 jQuery 的一个简写形式，例如 `$("#syl")` 和 `jQuery("#syl")` 是等价的，`$.ajax` 和 `jQuery.ajax` 是等价的，如果没有特别说明，程序中的 `$` 符号都是 jQuery 的一个简写形式。

#### 5.1 文档就绪函数

所有 jQuery 函数位于一个 document ready 函数中：

```js
$(document).ready(function(){

});

// 可以简写成

$(funciton(){

});
```

这是为了防止文档在完全加载（就绪）之前运行 jQuery 代码。如果在文档没有完全加载之前就运行函数，操作可能失败。下面是两个具体的例子：

+ 试图隐藏一个不存在的元素。
+ 获得未完全加载的图像的大小。

上面的这段代码其实有点类似于传统 JavaScript 中的 window.onload 方法，不过它们还是有一些区别的，简单对比如下所示：

|          | window.onload                                          | $(doucment).ready()                                          |
| -------- | ------------------------------------------------------ | ------------------------------------------------------------ |
| 执行时机 | 必须等待网页中所有的内容加载完毕后才能执行（包括图片） | 网页中所有 DOM 结构绘制完毕后就执行，可能 DOM 元素关联的东西并没有加载完 |
| 编写个数 | 不能同时编写多个。                                     | 能同时编写多个。                                             |

编写个数的意思就是：

```js
window.onload = function () {
  alert("test1");
};
window.onload = function () {
  alert("test2");
};
//结果只会输出 test2。
$(document).ready(function () {
  alert("test1");
});
$(document).ready(function () {
  alert("test2");
});
//结果两次都输出
```

#### 5.2 编写我们的第一个 jQuery 程序

例子：

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title></title>
    <!-- Google 的 CDN 的方式加载jQuery，请大家自行修改为本地 -->
    <script
      type="text/javascript"
      src="http://ajax.googleapis.com/ajax/libs/jquery/1.4.0/jquery.min.js"
    ></script>
  </head>
  <body>
    <script type="text/javascript">
      //等待dom元素加载完毕
      $(document).ready(function () {
        //弹出一个框:显示hello syl
        alert("hello syl");
      });
    </script>
  </body>
</html>
```

> 请大家将加载 jQuery 的 Google 的 CDN 改为我们之前讲到的引入本地的 jQuery 文件，请大家将它下载下来然后做一个替换，因为在环境内无法访问到这些 CDN，之后的实验中同理，不再赘述。

> 修改内容为，将`<script type="text/javascript" src="http://ajax.googleapis.com/ajax/libs/jquery/1.4.0/jquery.min.js"></script>`修改为`<script type="text/javascript" src="jquery.min.js"></script>`。这里需要使用前面的命令，将 jquery.min.js 下载到与代码文件同一路径目录下。

运行效果为：