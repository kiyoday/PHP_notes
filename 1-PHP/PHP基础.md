## PHP引用变量

### 引用变量的概念及定义

- 概念：意味着用不同的名字访问同一个变量内容
- 定义方式：&符号

### PHP引用变量的原理

- 工作原理：引用一个变量相当于两个变量指向一个地址，改一个全改

- **unset( )只会取消引用、不会销毁空间**🌌

  ```php
  $a = range(0,1000);
  var_dump(memory_get_usage());
  
  $b = $a;//a、b指向同一地址
  var_dump(memory_get_usage());//没有开辟
  
  $a = range(0,1000);	//写实复制会新开辟空间，a、b指向不同地址
  var_dump(memory_get_usage());//开辟了
  ```

  <img src="https://i.loli.net/2020/06/03/NTvQoXnliA2xtVs.png" alt="image-20200603232126412" style="zoom: 50%;" />

- 引用传递

  ```php
  $a = 'hello';
  $b = &$a; 	//不会开辟新的空间
  $a = 'world';//a、b指向同一地址 不会开辟新空间
  
  unset($b);	//$a还存在
  xdebug_debug_zval($a);//查看容器
  ```

  <img src="https://i.loli.net/2020/06/03/SQuMOFRkK3BqUcf.png" alt="image-20200603232402713" style="zoom: 33%;" />

- **对象本身就是引用传递**

  ```php
  class Person{
      public $name = "zhangsan";
  }
  $p1 = new Person;
  $p2 = $p1;
  
  $p2->name="lisi";
  ```

  ![image-20200603232719560](C:\Users\12605\AppData\Roaming\Typora\typora-user-images\image-20200603232719560.png)

- foreach($data as $key => $val) {循环体}  循环遍历数组

  ```php
  $data=['a','b','c'] ;
  foreach($data as $key => $val)
  {
  	$val = &$data [$key];
  }
  ```

  <img src="https://i.loli.net/2020/06/04/Scywg24Balj5YO7.png" alt="image-20200603233135674" style="zoom:50%;" />

## 常量及数据类型

### 字符串定义方式及区别

- 定义方式

  - 单引号

  - 双引号

    ```php
    $str = 'abcdef $a g';
    $str ='abcdef '{$a}'g h”
    ```

  - heredoc和newdoc

    ```php
    echo <<<EOF
            <h1>我的第一个标题</h1>
            <p>我的第一个段落。</p>
    EOF;
    // 结束需要独立一行且前后不能空格
    ```

    

- 区别

  - 单引号

  	- 单引号不能解析变量，效率高于双引号
  	- 变量和变量、变量和字符串、字符串和字符串之间可以用“ . ”连接
  	- 单引号**<u>不能解析转义字符</u>**，只能解析单引号和反斜线本身

  - 双引号

  	- 双引号**可以解析变量**，变量可以使用特殊字符和“{}”包含
  	- 双引号可以解析所有**转义字符**
  	- 也可以使用“ . ”来连接

  - Heredoc和Newdoc

  	- Heredoc类似于双引号
  	- Newdoc类似于单引号
  	- 两者都用来处理大文本

### 数据类型

- 整型
- 浮点✨

  **浮点类型不能用于比较**中，尤其是相等（计算器计算时会转成二进制造成损耗）
   ![image-20200603190715659](https://i.loli.net/2020/06/03/Uat7yR6B4VCYEu9.png)

- 字符串
- 布尔型

  FALSE的七种情况：💡

  整型0、浮点0.0、布尔false，空字符串、0字符串、空数组、NULL

- 数组

  - **超全局数组**🧵

    $\_GLOBALS、$\_GET、$\_POST、$\_REQUEST、

    $\_SESSION、$\_COOKIE、$\_SERVER、$\_FILES、$\_ENV

    ```php
    $_SERVER['REMOTE_ADDR'] //当前浏览用户的IP地址
    $_SERVER['SERVER_ADDR'] //当前运行脚本所在服务器的IP地址
    ```

- 对象

- 空值NULL

  - 三种情况💡

    直接赋值为NULL、未定义的变量、unset销毁的变量

- 资源

  

### 常量

- 定义const/define

	  ```php
	const pi=3.14
	define('pi',3.14);
	```
	
	- const更快（是语言结构）define是**函数**💡
	
	- define不能用于**类常量**的定义，但const可以
	
	- 常量一经定义，不能被修改，不能被删除
	
- 预定义常量 魔术常量🎭

  ```php
  __LINE__;	//文件中的当前行号
  __FILE__;	//文件的完整路径和文件名
  __DIR__;	//文件所在的目录
  __FUNCTION__;//函数名称
  __CLASS__ ;  //类的名称
  __METHOD__; //类的方法名
  __NAMESPACE__; //当前命名空间的名称
  ```

  

## 运算符

### 错误控制符@

- @：当将其放置在一个PHP表达式之前，该表达式可能<u>产生的任何错误信息都被忽略掉</u>

  ```php
  foo();
  @foo();
  ```

  

### 所有运算符

- 运算符优先级

  <img src="https://i.loli.net/2020/06/04/mesVN1pUHhoajiz.png" alt="image-20200604100643560" style="zoom: 50%;" />

- 比较运算符

  - ==与===的区别

    <u>==用于数值，===还用于数据类型</u>

  - 等值判断（FALSE的七种情况）

- 递增递减运算符

	- 递增/递减运算符不影响布尔值
	- 递减NULL值没有效果
	- 递增NULL值为1
	- 递增递减在前先运算后返回，反之先返回再运算

- 逻辑运算符

	- 短路作用

	- ||和&&与or和and的优先级不同
	
	  <img src="https://i.loli.net/2020/06/04/ytwKYoLQnusPRe2.png" alt="image-20200604101011931" style="zoom:50%;" />

## 流程控制

### 遍历数组的三种方式及各自区别

- for循环

	- 只能遍历**索引数组**

- foreach循环

	- 可以遍历**索引和关联**数组
	- 会对数组进行reset( ) 

- while、list( )、each( )组合循环

	- 可以遍历索引和关联数组
	- 不会reset( )重置指针

### 分支结构

- if......elseif

- switch..case...

	- 不会一层一层判断

## 自定义函数及内部函数

### 变量的作用域和静态变量

- 变量的作用域

	- 局部变量和全局变量

		- global关键字
		- $GLOBALS及其他超全局数组

- 静态变量

	- static关键字

### 外部文件的导入

### 函数的返回值及引用返回

- 函数的返回值

	- 值通过使用可选的返回语句(return  )返回
	- 可以返回包括数组和对象的任意类型
	- 返回语句会终止函数执行，将控制权交回函数调用处
	- 省略return则返回值为NULL
	- 不可以有多个返回值

- 引用返回

### 系统内置函数🏆

- **时期日期函数**
  - date()	格式化本地日期和时间（farmat, timestamp）
  - time()	返回当前时间的 Unix 时间戳。
  - mktime()   返回日期的 Unix 时间戳
  - strtotime()	将任何英文文本的日期或时间描述解析为 Unix 时间戳
  - microtime()	返回当前时间的微秒数。
  - date_default_timezone_set()	设置由所有的 Date/Time 函数使用的默认时区。

- IP处理函数

	- ip2long — 将 IPV4 的字符串互联网协议转换成长整型数字
	- long2ip — 将长整型转化为字符串形式带点的互联网标准格式地址（IPV4）

- 打印处理

  - echo — 输出一个或**多个字**符串（可以打印多个变量，没有返回值，速度快）

  - print — 只能输出**一个字符串**，且返回值总为1

  - print_r — 打印复合类型、如数组和对象

  - printf — 输出格式化字符串（根据格式输出）

  - sprintf - 返回格式化的字符串返回不输出

  - var_dump — 打印变量的相关信息（打印类型用于debug）

  - var_export — 输出或返回一个变量的字符串表示（符合语法结构可以直接使用）

- 序列化及反序列化

	- serialize — 产生一个可存储的值的表示
	- unserialize — 从已存储的表示中创建 PHP 的值

- **字符串处理函数**🎈
  - strlen — 获取字符串长度
  - implode — 将一个一维数组的值转化为字符串
  - explode — 使用一个字符串分割另一个字符串
  - strrev — 反转字符串
  - strstr — 查找字符串的首次出现
  - trim — 去除字符串首尾处的空白字符（或者其他字符）
  - ltrim — 删除字符串开头的空白字符（或其他字符）
  - rtrim — 删除字符串末端的空白字符（或者其他字符）
  - number_format — 以千位分隔符方式格式化一个数字（处理美元）

- **数组处理函数**💎
  - sort — 对数组排序
  - count — 计算数组中的单元数目，或对象中的属性个数
  - array_keys — 返回数组中部分的或所有的键名
  - array_values — 返回数组中所有的值
  - array_diff — 计算数组的差集
  - array_intersect — 计算数组的交集
  - array_merge — 合并一个或多个数组
  - array_shift — 将数组开头的单元移出数组
  - array_unshift — 在数组开头插入一个或多个单元
  - array_pop — 弹出数组最后一个单元（出栈）
  - array_push — 将一个或多个单元压入数组的末尾（入栈）

## 正则表达式🏆

### 作用

分割、查找、匹配、替换字符串

### 常用函数

```php
preg_match($pattern, $subject);
preg_match_all($pattern, $subject, array &$matches);
preg_replace($pattern, $replacement, $subject);
preg_filter($pattern, $replacement, $subject);
preg_grep($pattern, array $input);
preg_split($pattern, $subject);
preg_quote($str);

function show($var){
    if(empty($var)){
        echo 'null';
    }elseif(is_array($var)||is_object($var)){
        echo '<pre>';
        print_r($var);
        echo '</pre>'
    }else{
        echo $var;
    }
}
```

- preg_match()与preg_match_all() 都是引用传递

![image-20200603205555758](https://i.loli.net/2020/06/03/GxqlnbAYJpUeB6r.png)





### 组成

- 分隔符

- 通用原子

	- \d	 匹配一个数字字符。等价于 [0-9]
	- \D	匹配一个非数字字符。等价于 [^0-9]
	- \w	匹配字母、数字、下划线。等价于'[A-Za-z0-9_]'
	- \W	匹配非字母、数字、下划线。等价于 '[^A-Za-z0-9_]'
	- \s	匹配任何空白字符，包括空格、制表符、换页符等等。等价于 [ \f\n\r\t\v]
	- \S	匹配任何非空白字符。等价于 [^ \f\n\r\t\v]

- 元字符

	- .	匹配除换行符（\n、\r）之外的任何单个字符。
	- ^	匹配输入字符串的开始位置。
	- $	匹配输入字符串的结束位置。
	- (pattern)	匹配 pattern 并获取这一匹配。
	- *	匹配前面的子表达式零次或多次。例如，zo* 能匹配 "z" 以及 "zoo"。* 等价于{0,}。
	- ?	匹配前面的子表达式零次或一次。例如，"do(es)?" 可以匹配 "do" 或 "does" 。? 等价于 {0,1}。
	- +	匹配前面的子表达式一次或多次。例如，'zo+' 能匹配 "zo" 以及 "zoo"，但不能匹配 "z"。+ 等价于 {1,}。
	- {n}	n 是一个非负整数。匹配确定的 n 次。例如，'o{2}' 不能匹配 "Bob" 中的 'o'，但是能匹配 "food" 中的两个 o。
	- {n,}	n 是一个非负整数。至少匹配n 次。例如，'o{2,}' 不能匹配 "Bob" 中的 'o'，但能匹配 "foooood" 中的所有 o。'o{1,}' 等价于 'o+'。'o{0,}' 则等价于 'o*'。
	- {n,m}	m 和 n 均为非负整数，其中n <= m。最少匹配 n 次且最多匹配 m 次。例如，"o{1,3}" 将匹配 "fooooood" 中的前三个 o。'o{0,1}' 等价于 'o?'。
	- [xyz]	字符集合。匹配所包含的任意一个字符。例如， '[abc]' 可以匹配 "plain" 中的 'a'。
	- x|y	匹配 x 或 y。例如，'z|food' 能匹配 "z" 或 "food"。'(z|f)ood' 则匹配 "zood" 或 "food"。
	- [^xyz]	负值字符集合。匹配未包含的任意字符。例如， '[^abc]' 可以匹配 "plain" 中的'p'、'l'、'i'、'n'。
	- [a-z]	字符范围。匹配指定范围内的任意字符。例如，'[a-z]' 可以匹配 'a' 到 'z' 范围内的任意小写字母字符。

- 模式修正符

### 后向引用

### 贪婪模式

### 正则表达式PCRE函数

- preg_match	执行一个正则表达式匹配
- preg_match_all	执行一个全局正则表达式匹配
- preg_replace	执行一个正则表达式的搜索和替换
- preg_split	通过一个正则表达式分隔字符串

### 中文匹配

## 文件及目录处理

### 文件读取/写入操作

- fopen — 打开文件或者 URL

- 写入函数

	- fwrite — 写入文件（可安全用于二进制文件）

- 读取函数

	- fread — 读取文件（可安全用于二进制文件）
	- fgets — 从文件指针中读取一行
	- fgetc — 从文件指针中读取字符

- 关闭文件函数

	- fclose — 关闭一个已打开的文件指针

- 不需要fopen()的函数

	- file_put_contents — 将一个字符串写入文件
	- file_get_contents — 将整个文件读入一个字符串

- 其他读取函数

	- file — 把整个文件读入一个数组中
	- readfile — 输出文件

- 访问远程文件

### 目录操作函数、其他文件操作

## 会话控制

### 通过GET参数传递

- 产生安全、参数丢失问题

### HTTP是无状态的，没有内建机制来保存状态，请求会被隔离，无法保持登录状态

### 会话控制技术

- cookie：存储在客户端的文件

	- cookie的操作

		- 创建setcookie($name,$value,$expire,$path,$domain,$secure);
		- 读取$_COOKIE
		- 删除：改$expire过期时间

	- 优点

		- 不会占用服务器资源，不会浪费

	- 缺点

		- 信息保存在客户端，有安全问题，用户也可以禁用

- session:存储在服务器

	- 基于cookie，有ID（存储在cookie）
	- session的操作

		- 启动session_start()启动会话

			- 函数必须位于 <html> 标签之前

		- 存储$_SESSION存储 Session 变量

		- 读取/遍历$_SESSION
		- 销毁可以使用 unset() 或 session_destroy() 函数

		- 配置

		- 优点和缺点

	- 传递SessionID的问题

		- session_name()
		- session_id()
	- session存储

		- 不要存储在文件中，应该存储在内存服务器中
		- 函数session_set_save_handle()
		- MySQL、Memcache、Redis等

## 面向对象

### 类权限控制修饰符

- public

	- 修饰的成员具有最高权限、可以在类的内部外部使用、供子类使用

- protected

	- 可以在类的内部使用、子类使用、可以继承但是不能在类的外部使用

- private

	- 只能在类的内部使用、不能被继承

### 面向对象的封装、继承和多态

- 封装

	- 成员访问权限

		- 封装是指将现实世界中存在的某个客体的属性与行为绑定在一起，并放置在一个逻辑单元内

- 继承

	- 单一继承

		- 同时只能继承一个父类

	- 子类拥有与父类同名方法会进行覆盖

		- 如果从父类继承的方法不能满足子类的需求，可以对其进行改写

- 多态

	- 抽象类

		- 任何一个类，如果它里面至少有一个方法是被声明为抽象的，那么这个类就必须被声明为抽象的
		- 定义为抽象的类不能被实例化
		- 被定义为抽象的方法只是声明了其调用方式（参数），不能定义其具体的功能实现
		- 继承一个抽象类的时候，子类必须定义父类中的所有抽象方法

	- 接口

		- 要实现一个接口，使用 implements 操作符
		- 使用接口（interface），可以指定某个类必须实现哪些方法，但不需要定义这些方法的具体内容
		- 接口是通过 interface 关键字来定义的，就像定义一个标准的类一样，但其中定义所有的方法都是空的
		- 接口中定义的所有方法都必须是公有，这是接口的特性

### 魔术方法

- __construct()，类的构造函数
- __destruct()，类的析构函数
- __call()，在对象中调用一个不可访问方法时调用
- __callStatic()，用静态方式中调用一个不可访问方法时调用
- __get()，获得一个类的成员变量时调用
- __set()，设置一个类的成员变量时调用
- __isset()，当对不可访问属性调用isset()或empty()时调用
- __unset()，当对不可访问属性调用unset()时被调用。
- __sleep()，执行serialize()时，先会调用这个函数
- __wakeup()，执行unserialize()时，先会调用这个函数
- __toString()，类被当成字符串时的回应方法
- __invoke()，调用函数的方式调用一个对象时的回应方法
- __set_state()，调用var_export()导出类时，此静态方法会被调用。
- __clone()，当对象复制完成时调用
- __autoload()，尝试加载未定义的类
- __debugInfo()，打印所需调试信息

### 设计模式

- 工厂模式

	- 我们定义一个专门用来创建其它对象的类。 这样在需要调用某个类的时候，我们就不需要去使用new关键字实例化这个类，而是通过我们的工厂类调用某个方法得到类的实例。
	- 好处：当我们对象所对应的类的类名发生变化的时候，我们只需要改一下工厂类类里面的实例化方法即可。不需要外部改所有的地方

- 单例模式

	- 可用于数据库创建，只允许new一个数据库类
	- 单例模式的最大好处就是减少资源的浪费，保证整个环境中只存在一个实例化的对象，特别适合资源连接类的编写

- 注册树模式

	- 已经创建好对象后，下次使用直接取，将一些对象注册到全局树上面，可以用来在任何地方被访问

- 适配器模式

	- 可以将截然不同的函数接口封装成统一的API
	- 使用适配器策略是为了更好的兼容

- 策略模式

	- 将一组特定的行为和算法封装成类，以适应某些特定的上下文环境

- 观察者模式

	- 当一个对象状态发生改变时，依赖它的对象全部会收到通知，并自动更新
	- 观察者模式实现了低耦合，非入侵式的通知与更新机制

## 网络协议

### HTTP协议状态码

- 五类响应

	- 1XX	100-101	信息提示
	- 2XX	200-206	成功
	- 3XX	300-305	重定向
	- 4XX	400-415	客户端错误
	- 5XX	500-505	服务器错误

- 常见协议码

	- 200 OK 服务器成功处理了请求（这个是我们见到最多的）
	- 301/302 Moved Permanently（重定向）请求的URL已移走。Response中应该包含一个Location URL, 说明资源现在所处的位置
	- 304 Not Modified（未修改）客户的缓存资源是最新的， 要客户端使用缓存
	- 404 Not Found 未找到资源
	- 501 Internal Server Error服务器遇到一个错误，使其无法对请求提供服务

### OSI七层模型

- 第一层 物理层

	- 建立、维护、断开物理连接

- 第二层 数据量链路层

	- 建立逻辑连接、进行硬件地址寻址、差错校验等功能

- 第三层 网络层

	- 进行逻辑地址寻址，实现不同网路之间的路径选择

- 第四次 传输层

	- 定义传输数据的协议端口号，以及流控和差错校验
	- 协议有TCP、UDP，数据包一旦离开网卡即进入网络传输层

- 第五层 会话层

	- 建立、管理、终止会话

- 第六次 表示层

	- 数据的表示、安全、压缩

- 第七层 应用层

	- 网络服务与最终用户的一个接口
### HTTP协议的工作特点和工作原理

- 工作特点

- 工作原理

### HTTP协议常见请求/响应头和请求方法

- 常见请求/响应头

- 请求方法

	- GET和POST的区别

### HTTPS协议的工作原理

### 常见网络协议含义及端口

## 开发环境及配置

### 版本控制软件

- 集中式

	- 版本上传到中央服务器
	- CVS和SVN

- 分布式

	- Git

### PHP运行原理

- Nginx+PHP-FPM
- CGI协议  PHP解析器 与webserver通讯、联系
- FastCGI 是CGI的改良版本 可以保留进程
- PHP-FPM 是FastCGI的进程管理器

### PHP常见配置项

