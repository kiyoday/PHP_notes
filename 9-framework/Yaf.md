# 简介

PHP框架层出不穷，比如laravel，symfony，Zend Framework，codeigniter，国内的thinkphp等。

在做项目的时候使用框架的好处是提高开发效率，缺点就是损失了性能。

**以下是鸟哥的自白：**

也就是说， 有没有那么一个框架， 既不会有损性能， 又能提高开发效率呢.

Yaf， 就是为了这个目标而生的.

Yaf有着和Zend Framework相似的API， 相似的理念， 而同时又保持着对Bingo的兼容， 以此来提高开发效率， 规范开发习惯. 本着对性能的追求， Yaf把框架中不易变的部分抽象出来，采用PHP扩展实现(c语言)，以此来保证性能.在作者自己做的简单测试中， Yaf和原生的PHP在同样功能下，性能损失小于10%， 而和Zend Framework的对比中， Yaf的性能是Zend Framework的50-60倍.

## Yaf的优点

1. 用C语言开发的PHP框架，相比原生的PHP，几乎不会带来额外的性能开销.
2. 所有的框架类， 不需要编译， 在PHP启动的时候加载， 并常驻内存.
3. 更短的内存周转周期， 提高内存利用率， 降低内存占用率.
4. 灵巧的自动加载。支持全局和局部两种加载规则， 方便类库共享.
5. 高性能的视图引擎.
6. 高度灵活可扩展的框架，支持自定义视图引擎，支持插件， 支持自定义路由等等.
7. 内建多种路由，可以兼容目前常见的各种路由协议.
8. 强大而又高度灵活的配置文件支持。并支持缓存配置文件，避免复杂的配置结构带来的性能损失。
9. 在框架本身，对危险的操作习惯做了禁止。
10. 更快的执行速度，更少的内存占用。

##  Yaf的安装

Yaf只支持PHP5.2及以上的版本

Yaf需要SPL的支持. SPL在PHP5中是默认启用的扩展模块

Yaf需要PCRE的支持. PCRE在PHP5中是默认启用的扩展模块

### 在 Windows 系统下安装

PHP 5.2+

1. 打开yaf在php官网上的目录：http://pecl.php.net/package/yaf
2. 目前yaf的最新版为3.0.0，仅支付php7，建议选择2.3.5版本
3. 我这里选择2.3.5后面的win图标+DLL字样的链接，进入页面下载php_yaf.dll
4. 在打开的页面根据自己的环境来选择对应的版本，我这里选择的是php5.6 Thread Safe (TS) x86(php5.6版本 安全线程 32位操作系统)
   ![img](https://box.kancloud.cn/2015-11-25_56555e23c65d1.png)
5. 点击后自动下载了一个压缩包：php_yaf-2.3.5-5.6-ts-vc11-x86.zip
6. 把压缩包中的php_yaf.dll复制出来，打到你的php目录，打开目录下的ext文件夹，粘贴进去
7. 再打开您的PHP配置文件php.ini，加入 'extension=php_yaf.dll'，重启web服务器，就OK了
   ![img](https://box.kancloud.cn/2015-11-25_56555e2960975.png)

### 在 Linux 系统下安装

下载Yaf的最新版本， 解压缩以后， 进入Yaf的源码目录， 依次执行(其中PHP_BIN是PHP的bin目录):

> $PHP_BIN/phpize
> ./configure --with-php-config=$PHP_BIN/php-config
> make
> make install

[然后在php.ini中载入yaf.so](http://xn--php-u33ew5lnq3c.xn--iniyaf-dw7iw3ts50n.so/)， 重启PHP.

Yaf_Request_Abstract的getPost， getQuery等方法， 并没有对应的setter方法. 并且这些方法是直接从PHP内部的$_POST、 $_GET等大变量的原身变量只读的查询值， 所以就有一个问题:通过在PHP脚本中对这些变量的修改， 并不能反映到getPost/getQuery等方法上.

## Hello Yaf

### 目录结构

一个典型的目录结构

> - public
>   |- index.php //入口文件
>   |- .htaccess //重写规则
>   |+ css
>   |+ img
>   |+ js
> - conf
>   |- application.ini //配置文件
> - application
>   |+ controllers
>   |- Index.php //默认控制器
>   |+ views
>   |+ index //控制器
>   |- index.phtml //默认视图
>   |+ modules //其他模块
>   |+ library //本地类库
>   |+ models //model目录
>   |+ plugins //插件目录

### 入口文件

几乎所有php框架都采用了单一入口方式，入口文件是所有请求的入口， 一般都借助于rewrite规则， 把所有的请求都重定向到这个入口文件.

#### 一个经典的入口文件public/index.php

```php
<?php 
	define("APP_PATH"， realpath(dirname(__FILE__) . '/../')); 
	/* 指向public的上一级 */ 
	$app = new Yaf_Application(APP_PATH . "/conf/application.ini"); 
	$app->run();
```

### 配置文件

在Yaf中， 配置文件支持继承， 支持分节. 并对PHP的常量进行支持. 你不用担心配置文件太大造成解析性能问题， 因为Yaf会在第一个运行的时候载入配置文件， 把格式化后的内容保持在内存中. 直到配置文件有了修改， 才会再次载入.

#### 一个简单的配置文件application/conf/application.ini

```
[product] ;支持直接写PHP中的已定义常量 application.directory=APP_PATH "/application/"
```

### 控制器

在Yaf中， 默认的模块/控制器/动作， 都是以Index命名的， 当然，这是可通过配置文件修改的.
对于默认模块， 控制器的目录是在application目录下的controllers目录下， Action的命名规则是"名字+Action"

#### 默认控制器application/controllers/Index.php

```
<?php class IndexController extends Yaf_Controller_Abstract { public function indexAction() {//默认Action $this->getView()->assign("content"， "Hello Yaf"); } } ?>
```

### 视图文件

Yaf支持简单的视图引擎， 并且支持用户自定义自己的视图引擎， 比如Smarty.
对于默认模块， 视图文件的路径是在application目录下的views目录中以小写的action名的目录中.



html在这个文档里不显示，我这里截了一张图
![![img](https://box.kancloud.cn/2015-11-25_565581ce6940c.png)]

### 运行

在浏览器输入 http://yourhostname/public/index.php

是不是看到了页面输出的Hello Yaf

### 额外说明一下：

必要的配置项
Yaf和用户共用一个配置空间， 也就是在Yaf_Application初始化时刻给出的配置文件中的配置. 作为区别， Yaf的配置项都以ap开头. Yaf的核心必不可少的配置项只有一个(其实， 这个也可以有默认参数， 但是作者觉得完全没有配置， 显得太寒酸了).
application.directory String 应用的绝对目录路径

就是我们刚在application/conf/application.ini里配置的内容

### Yaf的项目可选配置信息

详情可以稳步到yaf文档查看http://www.laruence.com/manual/yaf.config.optional.html

## 自动加载器

Yaf在自启动的时候， 会通过SPL注册一个自己的Autoloader， 出于性能的考虑， 对于框架相关的MVC类， Yaf Autoloader只以目录映射的方式尝试一次.

#### Yaf目录映射规则

| 类型     | 后缀       | 映射路径                                                     |
| :------- | :--------- | :----------------------------------------------------------- |
| 控制器   | Controller | 默认模块下为{项目路径}/controllers/, 否则为{项目路径}/modules/{模块名}/controllers/ |
| 数据模型 | Model      | {项目路径}/models/                                           |
| 插件     | Plugin     | {项目路径}/plugins/                                          |

#### 一个简单的自我理解

```php
<?php 
class IndexController extends Yaf_Controller_Abstract { 
	public function indexAction() {
		//默认
		Action $mod = new TserModel(); //自动加载model下面的
        test.php文件 $mod->query(); //调用TestModel里的query方法 
        $user = new UserPlugin(); //自动加载plugins下面的user.php文件 
        $this->getView()->assign("title"， "Hello Yaf"); 
        $this->getView()->assign("content"， "Hello Yaf 				Content"	); 
    }
```

## 类的加载规则

而类的加载规则， 都是一样的: Yaf规定类名中必须包含路径信息， 也就是以下划线"_"分割的目录信息. Yaf将依照类名中的目录信息， 完成自动加载. 如下的例子， 在没有申明本地类的情况下:

```php
public function indexAction() {
    $upload = new upload_aliyun(); //这个就会按下划线分割目录来寻找文件，所以他会寻找 \library\upload\aliyun.php

}`
```

先这么简单理解，还有一个registerLocalNamespace的内容，后续再来说一说，怕混了。

### 手动载入

Yaf_Loader::import
导入一个PHP文件， 因为Yaf_Loader::import只是专注于一次包含， 所以要比传统的require_once性能好一些
示例：
`<?php //绝对路径 Yaf_Loader::import("/usr/local/foo.php); //相对路径， 会在APPLICATION_PATH."/library"下加载 Yaf_loader::import("plugins/User.php"); ?>`

Bootstrap， 也叫做引导程序. 它是Yaf提供的一个全局配置的入口， 在Bootstrap中， 你可以做很多全局自定义的工作.

### 使用Bootstrap

在一个Yaf_Application被实例化之后， 运行(Yaf_Application::run)之前， 可选的我们可以运行Yaf_Application::bootstrap

#### 改写index.php文件如下：

```php
<?php define("APP_PATH"， realpath(dirname(__FILE__))); $app = new Yaf_Application(APP_PATH . "/conf/application.ini"); $app->bootstrap()->run();
```

当bootstrap被调用的时刻， Yaf_Application就会默认的在APPLICATION_PATH下， 寻找Bootstrap.php， 而这个文件中， 必须定义一个Bootstrap类， 而这个类也必须继承自Yaf_Bootstrap_Abstract.

实例化成功之后， 所有在Bootstrap类中定义的， 以_init开头的方法， 都会被依次调用， 而这些方法都可以接受一个Yaf_Dispatcher实例作为参数.

也可以通过在配置文件中修改application.bootstrap来变更Bootstrap类的位置.

### 简单的示例Bootstrap.php

```php
<?php class Bootstrap extends Yaf_Bootstrap_Abstract{ public function _initConfig() { $config = Yaf_Application::app()->getConfig(); Yaf_Registry::set("config"， $config); } public function _initDefaultName(Yaf_Dispatcher $dispatcher) { $dispatcher->setDefaultModule("Index")->setDefaultController("Index")->setDefaultAction("index"); } }
```