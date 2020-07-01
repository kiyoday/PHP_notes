# Web 安全防范

 ## SQL注入
SQL注入是一种恶意攻击，用户利用在表单字段输入SQL语句的方式来影响正常的SQL执行。

动态拼装SQL语句：

![img](https://box.kancloud.cn/05e5306cd5dadbdc1890bf98696d6a8f_641x303.png)

上面的代码，在第一行没有过滤或转义用户输入的值($_POST['username'])。因此查询可能会失败，甚至会损坏数据库，这要看$username是否包含变换你的SQL语句到别的东西上。

防止SQL注入：

- 使用`mysql_real_escape_string()`过滤数据
- 手动检查每一数据是否为正确的`数据类型`
- 使用`预处理语句`并绑定变量
- 使用准备好的预处理语句
- 分离数据和SQL逻辑
- 预处理语句将自动`过滤`(如：`转义`)
- 把它作为一个编码规范，可以帮助团队里的新人避免遇到以上问题

## XSS攻击

### XSS 是什么？如何防范？

XSS(Cross Site Scripting)，跨站脚本攻击，攻击者往 Web 页面里插入恶意 Script 代码，当用户浏览该页之时，嵌入其中 Web 里面的 Script 代码会被执行，从而达到恶意攻击用户的目的。

防止 XSS 攻击的方式有很多，其核心的本质是：永远不要相信用户的输入数据，始终保持对用户数据的过滤。



XSS（Cross Site Scripting：跨站脚本）是一种攻击，由用户输入一些数据到你的网站，其中包括客户端脚本(通常`JavaScript`)。如果你没有过滤就输出数据到另一个web页面，这个脚本将被执行.
接收用户提交的文本内容.

![img](https://box.kancloud.cn/d5519762bd1bb0a3f621a249ef48581d_732x292.png)

输出内容给(另一个)用户

```html
<form action='xss.php' method='POST'>
	Enter your comments here: <br />
	<textarea name='comment'></textarea> <br />
	<input type='submit' value='Post comment' />
</form>
<?php echo $comments; ?>
```

将会发生什么事?
烦人的弹窗，刷新或重定向，损坏网页或表单，窃取cookie，AJAX(XMLHttpRequest)

### 防止XSS攻击:

为了防止XSS攻击，使用PHP的`htmlentities()`函数过滤再输出到浏览器。htmlentities()的基本用法很简单，具体可查看下手册。

### 会话固定

会话安全，假设一个PHPSESSID很难猜测。然而，PHP可以接受一个会话ID通过一个Cookie或者URL。因此，欺骗一个受害者可以使用一个特定的(或其他的)会话ID 或者钓鱼攻击。

#### 会议捕获和劫持

这是与会话固定有着同样的想法，然而，它涉及窃取会话ID。如果会话ID存储在Cookie中，攻击者可以通过XSS和JavaScript窃取。如果会话ID包含在URL上，也可以通过嗅探或者从代理服务器那获得。

防止会话捕获和劫持:

- 更新ID
- 如果使用会话，请确保用户使用`SSL`

## 跨站点请求伪造(CSRF)

CSRF攻击，是指一个页面发出的请求，看起来就像是网站的信任用户，但不是故意的。它有许多的变体.
防止跨站点请求伪造,一般来说，确保用户来自你的表单，并且匹配每一个你发送出去的表单。有两点一定要记住：

- 对用户会话采用适当的安全措施，例如:给每一个会话更新id和用户使用SSL。
- 生成另一个一次性的令牌并将其嵌入表单，保存在会话中(一个会话变量)，在提交时检查它。

## 代码注入
代码注入是利用计算机漏洞通过处理无效数据造成的。问题出在，当你不小心执行任意代码，通常通过文件包含。写得很糟糕的代码可以允许一个远程文件包含并执行。如许多PHP函数，如require可以包含URL或文件名，例如：

```php+HTML
<form>
Choose theme:
	<select name = theme>
		<option value = blue>Blue</option>
		<option value = green>Green</option>
		<option value = red>Red</option>
	</select>
	<input type = submit>
	</form>
<?php
if($theme) {
         require($theme.'.txt');
}
```

在上面的例子中，通过传递用户输入的一个文件名或文件名的一部分，来包含以`http://`开头的文件。

### 防止代码注入

- 过滤用户输入

- 在php.ini中设置禁用`allow_url_fopen`和`allow_url_include`。

  这将禁用require/include/fopen的远程文件。

## 其他的一般原则:

- 不要依赖服务器配置来保护你的应用，特别是当你的web服务器/ PHP是由你的ISP管理，或者当你的网站可能迁移/部署到别处，未来再从别处迁移/部署在到其他地方。请在网站代码中嵌入带有安全意识的检查/逻辑(HTML、JavaScript、PHP,等等)。
- 设计服务器端的安全脚本:
  例如,使用单行执行 - 单点身份验证和数据清理
  例如,在所有的安全敏感页面嵌入一个PHP函数/文件，用来处理所有登录/安全性逻辑检查
- 确保你的代码更新，并打上最新补丁。

# 增强PHP安全的七个函数

> 永远不能相信那些用户输入的数据。

在PHP中，有些很有用的函数开源非常方便的防止你的网站遭受各种攻击，例如SQL注入攻击，XSS（Cross Site Scripting：跨站脚本）攻击等。
PHP中常用的确保安全的函数（注意，这并不是完整的列表）。

## addslashes() 

>  意为加斜杠

这个函数的原理跟mysql_real_escape_string()相似。但是当在php.ini文件中，“magic_quotes_gpc“的值是“on”的时候，就不要使用这个函数。`magic_quotes_gpc `的默认值是on，对所有的 GET、POST 和 COOKIE 数据自动运行 addslashes()。

不要对已经被 magic_quotes_gpc 转义过的字符串使用 addslashes()，因为这样会导致**双层转义**。你可以使用`get_magic_quotes_gpc()`函数来确定它是否开启。

## htmlentities()

这个函数对于过滤用户输入的数据非常有用。它会将一些特殊字符转换为HTML实体。
例如，用户输入`<`时，就会被该函数转化为HTML实体`<`，输入>就被转为实体`>`.

[HTML实体对表：](http://www.w3school.com.cn/html/html_entities.asp)
可以防止XSS和SQL注入攻击。

## htmlspecialchars()

在HTML中，一些特定字符有特殊的含义，如果要保持字符原来的含义，就应该转换为HTML实体。
这个函数会返回转换后的字符串，例如‘&’转为`&`（ps:请参照第二点中的实体对照表链接）

## strip_tags()

这个函数可以去除字符串中所有的HTML，JavaScript和PHP标签，当然你也可以通过设置该函数的第二个参数，让一些特定的标签出现。

## md5()

从安全的角度来说，一些开发者在数据库中存储简单的密码的行为并不值得推荐。
md5()函数可以产生给定字符串的32个字符的md5散列，而且这个过程不可逆，即你不能从md5()的结果得到原始字符串。
现在这个函数并不被认为是安全的，因为开源的数据库可以反向检查一个散列值的明文。你可以在这里找到一个MD5散列数据库列表

## sha1()

这个函数与md5()类似，但是它使用了不同的算法来产生40个字符的SHA-1散列（md5产生的是32个字符的散列）。
也不要把绝对安全寄托在这个函数上，否则会有意想不到的结果。

## intval()

intval()函数是将变量转成整数类型，我们可以用这个函数让你的PHP代码更安全，特别是当你在解析id，年龄这样的数据时。