# Laravel基础

## 一、框架

框提供了很多功能，比如数据库(DB)、缓存(Cache)、会话(Session)、文件上传等。

不但为前期开发提供了方便,更为后期项目性能的优化(缓存技术由文件缓存换成Redis ) ，

平台的改变(数据库由Oracle换成Mysql)提供了技术保障

## 二、目录结构

| 文件夹    | 介绍                                                    |
| --------- | ------------------------------------------------------- |
| app       | 网站的业务逻辑代码，例如：控制器/模型/路由等            |
| bootstrap | 框架启动与自动加载设置相关的文件                        |
| config    | 网站的各种配置文件                                      |
| database  | 数据库操作相关的文件                                    |
| public    | 网站的对外文件夹，入口文件和静态资源（CSS，JS，图片等） |
| resources | 前端视图文件和原始资源（CSS，JS，图片等）               |
| storage   | 编译后的视图、基于会话、文件缓存和其它框架生成的文件    |
| tests     | 自动化测试文件                                          |
| vendor    | Composer 依赖文件                                       |

除了上述文件夹，根目录下有些文件也比较常用：

| 文件          | 介绍                                                      |
| ------------- | --------------------------------------------------------- |
| .env          | 环境配置文件                                              |
| .env.example  | .env 文件的一个示例                                       |
| .gitignore    | git 的设置文件，制定哪些文件会被 git 忽略，不纳入文件管理 |
| composer.json | 网站所需的 composer 扩展包                                |
| composer.lock | 扩展包列表，确保这个网站的副本使用相同版本的扩展包        |
| gulpfile.js   | GULP 配置文件（ GULP 后边会学到）                         |
| package.json  | 网站所需的 npm 包                                         |
| readme.md     | 网站代码说明文件                                          |

有个简单的了解就好，在后续开发过程中会通过实践对 Laravel 的结构进行更深的理解。

# 初始强大的 Artisan

本次实验我们学习一下强大的 Artisan 命令。

## 一、什么是Artisan呢？

熟悉 linux 的朋友都知道，我们平时的创建/删除文件等常用操作都可以在命令行里来完成，Artisan 就相当于 Laravel 为我们独家定制的一个命令行工具，提供了很多实用的命令，可以用来快速生成 Laravel 开发中常用的一些文件并完成相关的配置。

## 二、常用 Artisan 命令

```bash
$ cd ~/Code/myweb
```

然后，执行 `php artisan list` 可以查看常用 artisan 命令：

```shell
$ php artisan list
```

下面列出最常用的一些命令，使用方法就像上面的 `php artisan list` 一样，先有个简单了解就好，我们在后面的开发中会多次使用到这些命令：

| 命令                         | 说明             |
| ---------------------------- | ---------------- |
| php artisan key:generate     | 生成 App Key     |
| php artisan make:controller  | 生成控制器       |
| php artisan make:model       | 生成模型         |
| php artisan make:policy      | 生成授权策略     |
| php artisan make:seeder      | 生成 Seeder 文件 |
| php artisan migrate          | 执行迁移         |
| php artisan migrate:rollback | 回滚迁移         |
| php artisan migrate:refresh  | 重置数据库       |
| php artisan db:seed          | 填充数据库       |
| php artisan tinker           | 进入 tinker 环境 |
| php artisan route:list       | 查看路由列表     |

## 三、基本路由

网站的大多数路由都定义在 app/Http/routes.php 文件中，该文件将会被 App\Providers\RouteServiceProvider 类加载。最基本的 Laravel 路由仅接受 URI 和一个闭包，下面我们再定义两条路由。

`app/Http/routes.php`

```php
Route::get('welcome', function () {
    return view('welcome');
});

Route::get('/', function() {
    return 'Index Page';
});

Route::get('/help', function() {
    return 'Help Page';
});
```

## 三、路由动作

我们知道，一个 url 请求可能有多种类型，除了常用的 GET，还可能有 POST、PUT、DELETE 等类型的请求。

对应的处理方法如下：

```
Route::post('/foo', function() {
    //该路由将匹配 post方法的 '/foo' url
});

Route::put('/foo', function() {
    //该路由将匹配 put方法的 '/foo' url
});
```

除此之外，还可以用 `match` 来同时处理多种类型的请求：

```
Route::match(['get', 'post'],'/foo', function () {
    // 该路由将匹配 get 和 post 方法的 'foo' url
});
```

甚至，还可以使用 `any` 来同时处理所有类型的请求：

```
Route::any('/foo', function() {
    // 该路由将匹配 所有 类型的 'foo' url
});
```

## 四、路由参数

### 1.基础的路由参数

有时候你可能需要从 URL 中获取一些参数，比如姓名、年龄等。

下面打开 routes.php，加入一个带参路由。

app/Http/routes.php

```php
Route::get('name/{name}', function ($name) {
    return 'I`m '.$name;
});
```

然后我们如果访问 `localhost/name/Tom` 我们可以看到网页返回了对应的信息：

如果试图访问 `localhost/name` 将会出现错误：



我们也可以定义多个参数：

app/Http/routes.php

```php
Route::get('name/{name}/age/{age}', function ($name, $age) {
    return 'I`m '.$name.' ,and I`m '.$age;
});
```

然后我们如果访问 `localhost/name/Tom/age/28` 我们可以看到网页返回了对应的信息：

### 2.可选的路由参数

有时你需要指定可选的路由参数，可以通过在参数后面加上 ? 来实现。

app/Http/routes.php

```php
Route::get('hello/{name?}', function ($name = null) {
    return 'Hello! '.$name;
});
```

这时你访问 `localhost/hello` 将不会报错，只是参数是空值。



你可以为该可选参数设定一个默认值，当 url 未传参时，将显示默认值。

`app/Http/routes.php`

```php
Route::get('hello/{name?}', function ($name = 'Tom') {
    return 'Hello! '.$name;
});
```

这时访问 `localhost/hello` 将返回默认的信息`hello! Tom`



如果访问`localhost/hello/John` 将会显示参数`hello! John`

## 五、命名路由

所谓命名路由，就是给路由起个名字，这样我们就可以通过这个名字获取到该条路由的相关信息，也更利于后期维护。

建议在开发过程中给每个路由命名，使用下面两种方式都可以为一个路由命名。

```php
Route::get('foo', ['as' => 'foo', function () {
    //
}]);

Route::get('foo', function() {
    //
})->name('foo');
```

## 六、路由群组

路由群组允许你共用路由属性，例如：中间件、命名空间等，你可以利用路由群组统一为多个路由设置共同属性，而不需在每个路由上都设置一次。共用属性被指定为数组格式，当作 `Route::group` 方法的第一个参数。

下面通过几个常用样例来熟悉这些特性。

### 1.为路由群组添加共用的中间件

> 至于什么是中间件，在后面的实验中会专门的学习。

```php
Route::group(['middleware' => 'auth'], function () {
    Route::get('/', function ()    {
        // 该路由将使用 Auth 中间件
    });

    Route::get('name', function () {
        // 该路由也将使用 Auth 中间件
    });
});
```

### 2.为路由群组添加共用的命名空间

> 什么是`命名空间`？从广义上来说，命名空间是一种封装事物的方法。在很多地方都可以见到这种抽象概念。例如，在操作系统中目录用来将相关文件分组，对于目录中的文件来说，它就扮演了命名空间的角色。具体举个例子，文件 foo.txt 可以同时在目录/home/greg 和 /home/other 中存在，但在同一个目录中不能存在两个 foo.txt 文件。另外，在目录 /home/greg 外访问 foo.txt 文件时，我们必须将目录名以及目录分隔符放在文件名之前得到 /home/greg/foo.txt。这个原理应用到程序设计领域就是命名空间的概念。

你可以简单的把命名空间理解为，`当前文件所在的位置`。

```php
Route::group(['namespace' => 'Admin'], function()
{
    // 控制器在「App\Http\Controllers\Admin」命名空间

    Route::group(['namespace' => 'User'], function()
    {
        // 控制器在「App\Http\Controllers\Admin\User」命名空间
    });
});
```

### 3.为路由群组添加共用的前缀

你可能有一系列路由都具有一个相同的前缀，比如：

```php
Route::get('user/name', function() {
    //
});
Route::get('user/age', function() {
    //
});
Route::get('user/introduction', function() {
    //
});
```

这时你就可以用路由群组来简化代码：

```php
Route::group(['prefix' => 'user'], function () {
    Route::get('name', function ()    {
        //
    });
    Route::get('age', function ()    {
        //
    });
    Route::get('introduction', function ()    {
        //
    });
});
```

路由群组也可以添加参数：

```php
Route::group(['prefix' => 'user/{id}'], function () {
    Route::get('name', function ($id)    {
        // 符合 'user/{id}/name' URL 
    });
    Route::get('age', function ($id)    {
        // 符合 'user/{id}/age' URL 
    });
});
```

## 八、查看路由

我们可以使用 `url('foo')` 函数来生成完整的 URL，例如，我们这样修改一下代码：

app/Http/routes.php

```php
Route::get('/help', function() {
    return url('/help');
});
```

然后，访问`localhost/help`,我们可以看到返回了完整的 URL 信息：

![图片描述](https://dn-simplecloud.shiyanlou.com/uid/ff207c4ac994ae597a753f238bd6b2de/1484724405195.png-wm)

我们还可以使用命名路由函数 `route('foo')` 来生成完整的 URL 信息。

app/Http/routes.php

```php
Route::get('/help', function() {
    return route('foo');
});

Route::get('/foo', function() {
    //
})->name('foo');

```

然后，访问 `localhost/help`，我们可以看到返回了 foo 路由的完整 URL 信息：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2484timestamp1483007098300.png/wm)

## 九、正则表达式限制路由

你可以使用 where 方法来限制参数的格式。where 方法接受参数的名称和正则表达式。

app/Http/routes.php

```php
Route::get('hello/{name?}', function ($name = 'Tom') {
    return 'Hello! '.$name;
})->where('name','[A-Za-z]+');
```

同样，也可对多个参数都设置正则验证：

app/Http/routes.php

```php
Route::get('name/{name}/age/{age}', function ($name, $age) {
    return 'I`m '.$name.' ,and I`m '.$age;
})->where(['name' => '[A-Za-z]+', 'age' => '[0-9]+']);
```

如果你想对所有的路由参数都加上某种限制，需要在 RouteServiceProvider 的 boot 方法里定义这些限制。

```php
/**
 * 定义你的路由模型绑定，模式过滤器等。
 *
 * @param  \Illuminate\Routing\Router  $router
 * @return void
 */
public function boot(Router $router)
{
    $router->pattern('id', '[0-9]+');

    parent::boot($router);
}
```

# 控制器

## 一、准备工作

首先，我们先创建一个路由，顺便再练习一下上次试验的内容，打开 `routes.php` 文件。

`app/Http/routes.php`

```php
<?php

Route::get('/', function () {
    return view('welcome');
});
Route::get('user/name', function() {
    return 'Name Page';
});
```

但是我们知道，实际的网站功能不可能这么单调，我们需要更多的返回信息和操作，显然把大量的处理代码写在这里是不合适的，这时候就要用到 Controller。

现在我们对上面的代码进行更改：

```php
Route::get('/', function () {
    return view('welcome');
});

Route::get('/user/name', 'UserController@name');
```

这段代码的意思就是，当用户访问 'localhost/user/name' 这个 URL 的时候，调用 UserController 这个控制器的 name 方法来处理请求。也就是说，将原来的闭包函数放到了一个单独的文件中。

我们可以将有共同特征的路由处理函数放到一个共同的控制器中，例如下面这种方式：

```php
Route::get('/user/name', 'UserController@name');
Route::get('/user/age', 'UserController@age');
Route::get('/user/introduction', 'UserController@introduction');
```

三条不同的路由的处理函数放在了同一个控制器（用户控制器）的三个不同的方法内。

下面我们通过几个简单的例子来体会一下 Controller 是怎么工作的。

## 二、基础控制器

控制器一般存放在 app/Http/Controllers 目录下，下面是一个基础控制器的例子，先来简单感受一下控制器文件的结构：

```php
namespace App\Http\Controllers;

use App\User;
use App\Http\Controllers\Controller;

class UserController extends Controller
{
    /**
     * 显示指定用户的个人数据。
     *
     * @param  int  $id
     * @return Response
     */
    public function show($id)
    {
        return view('user.profile', ['user' => User::findOrFail($id)]);
    }
}
```

所有 Laravel 控制器都应继承基础控制器类，它包含在 Laravel 的默认安装中。该基础类提供了一些便捷的方法，例如 middleware 方法（middleware 是中间件 后边会讲解），该方法可以用来将中间件附加在控制器行为上。

然后你就可以通过一个路由把请求引入到这个控制器文件来，像下面这样：

```php
Route::get('user/{id}', 'UserController@show');
```

现在，当请求和此特定路由的 URI 相匹配时，UserController 类的 show 方法就会被运行。当然，路由的参数也会被传递至该方法。

现在，让我们动手实践来感受一下控制器。

首先用 artisan 命令创建一个新的控制器，打开命令行，进入代码根目录：

```bash
cd ~/Code/myweb
```

然后运行 artisan 命令生成控制器：

```bash
php artisan make:controller UserController
```

然后转到 app/Http/Controllers 目录下，可以看到刚刚创建的 UserController.php。 打开这个文件：

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

use App\Http\Requests;
use App\Http\Controllers\Controller;

class UserController extends Controller
{
    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        //
    }

    /**
     * Show the form for creating a new resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function create()
    {
        //
    }

    /**
     * Store a newly created resource in storage.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return \Illuminate\Http\Response
     */
    public function store(Request $request)
    {
        //
    }

    /**
     * Display the specified resource.
     *
     * @param  int  $id
     * @return \Illuminate\Http\Response
     */
    public function show($id)
    {
        //
    }

    /**
     * Show the form for editing the specified resource.
     *
     * @param  int  $id
     * @return \Illuminate\Http\Response
     */
    public function edit($id)
    {
        //
    }

    /**
     * Update the specified resource in storage.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  int  $id
     * @return \Illuminate\Http\Response
     */
    public function update(Request $request, $id)
    {
        //
    }

    /**
     * Remove the specified resource from storage.
     *
     * @param  int  $id
     * @return \Illuminate\Http\Response
     */
    public function destroy($id)
    {
        //
    }
}
```

我们可以看到，Laravel 为我们生成了一些默认的代码，仔细观察可以发现是 7 个空方法，分别是

`index()` `create()` `store()` `show()` `edit` `update` `destroy`。

其中 `index()` 通常用来显示引导页/首页，其他的六个通常用来对数据的创建/读取/更新/删除操作，简称 CRUD （Create Retrieve Update Delete）。

现在我们先删除这些空操作，然后创建一个新方法：

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

use App\Http\Requests;
use App\Http\Controllers\Controller;

class UserController extends Controller
{
    public function name(){
        return 'Name Page';
    }
}
```

对应之前创建过的路由：

```php
Route::get('/user/name', 'UserController@name');
```

现在我们可以访问一下 `localhost/user/name` 看下效果：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2485timestamp1483084870750.png/wm)

可以看到，实现了跟以前一样的效果，但是我们应用了 Controller ，一个很简单的例子。

## 三、控制器的命名空间

可能你会注意到控制器文件中有这么一行代码：

```php
namespace App\Http\Controllers;
```

这行代码就是说明了此控制器的命名空间。

这个目录也是控制器的默认目录，所以我们在 routes.php 文件中引入的时候可以直接使用：

```php
Route::get('/user/name', 'UserController@name');
```

有一点非常重要，那就是我们在定义控制器路由时，不需要指定完整的控制器命名空间。我们只需要定义「根」命名空间 App\Http\Controllers 之后的部分类名称即可。默认 RouteServiceProvider 会使用路由群组，把 routes.php 文件里所有路由规则都配置了根控制器命名空间。

现在假如你在 App\Http\Controller 目录下新建了一个 User 文件夹来存放 UserControllser.php。

你的 routes.php 文件中就需要这么写：

```php
Route::get('/user/name', 'User\UserController@name');
```

相应的，控制器中的命名空间也要改变：

```php
namespace App\Http\Controllers\User;
```

现在让我们来删掉刚才的控制器，重新生成一个新的控制器，打开命令行：

```bash
rm app/Http/Controllers/UserController.php
```

然后重新生成一个 UserController ，这次我们单独创建一个 User 文件夹来存放这个 Controller 。

```bash
php artisan make:controller User/UserController
```

然后打开工程文件，我们可以看到，在 app\http\Controllers 文件夹下新建了一个 User 文件夹，里边有我们刚刚创建的 UserController.php 文件，打开这个文件：

```php
<?php

namespace App\Http\Controllers\User;

use Illuminate\Http\Request;

use App\Http\Requests;
use App\Http\Controllers\Controller;

class UserController extends Controller
{
    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        //
    }

    .
    .
    .
}
```

注意看命名空间，可以看到，默认生成的命名空间就是 App\Http\Controller\User 这就是 artisan 命令为我们做的。

修改为我们自己的方法：

```php
<?php

namespace App\Http\Controllers\User;

use Illuminate\Http\Request;

use App\Http\Requests;
use App\Http\Controllers\Controller;

class UserController extends Controller
{
    public function name(){
        return 'Name Page';
    }
}
```

然后对应更改 routes.php 文件：

```php
Route::get('/user/name', 'User\UserController@name');
```

然后访问 localhost/user/name 可以看到实现了正确的跳转！

## 四、控制器的依赖注入

细心的你肯定发现，控制器中还有几行神奇的代码：

```php
use Illuminate\Http\Request;

use App\Http\Requests;
use App\Http\Controllers\Controller;
```

这几行代码说明了该控制器的依赖注入，简单来说，依赖注入就是将该控制器用到的依赖添加进来。

比如，所有的 Controller 都依赖 基础 Controller.php ，所以需要：

```php
use App\Http\Controllers\Controller;
```

比如，当我们处理请求的时候，我们引入 Request 类，才可以使用很多 Laravel 提供的方法：

```php
use Illuminate\Http\Request;
```

在后边的实验中你会慢慢体会到依赖注入的强大之处。

# 中间件

本次实验我们学习中间件。

## 一、什么是中间件?

通过之前对路由和控制器的学习，我们知道一个请求可以通过路由分配到某个控制器上然后进行处理，如果我们想对请求加一个`限制`，只允许某些请求能够到达控制器，而过滤掉我们不想要的请求，这时候就可以使用 Laravel 的中间件。

例如，Laravel 自带的` Auth 中间件`可以用来验证用户的身份，如果用户未通过身份验证，中间件将会把用户导向登录页面，反之，当用户通过了身份验证，中间件将会通过此请求并接着往下执行。

当然了，除了验证，中间件也可以完成其他的各种各样的任务，如：`CORS 中间件`负责替所有即将离开程序的响应加入适当的标头；而`日志中间件`则可以记录所有传入应用程序的请求。

Laravel 框架已经内置了一些中间件，包括维护、身份验证、`CSRF 保护`，等等。所有的中间件都放在 app/Http/Middleware 目录内。

除了 Laravel 内置的中间件以外，我们也可以根据自己的需要创建中间件。

## 二、创建中间件

下面我们通过一个简单的例子来体会一下 midddleware 的工作过程。

思路如下：

1. 创建一个带参路由，用来产生一个请求，并且附带一个 age 参数；
2. 创建一个简单的中间件，用来过滤掉 age > 25 的用户，来实现一个简单的中间件过滤；
3. 未通过中间件的请求将被重定向到主页；
4. 通过中间件的的请求将达到指定的控制器，实现相应动作。

首先创建带参路由：

app/Http/routes.php

```php
Route::get('/', function () {
    return view('welcome');
});

Route::get('/young/{age}','UserController@young');
```

根据我们的思路：

- age > 25的路由如 `网址/young/56` 应该被过滤
- age <= 25的路由如 `网址/young/18` 应该被通过

然后创建中间件,我们使用 artisan 来创建：

```bash
cd ~/Code/myweb
php artisan make:middleware YoungMiddleware
```

![图片描述](https://dn-simplecloud.shiyanlou.com/uid/ff207c4ac994ae597a753f238bd6b2de/1484726700245.png-wm)

此命令将会在 `app/Http/Middleware` 目录内创建一个名称为 YoungMiddleware 的中间件。

在这个中间件内我们只允许请求的年龄 age 变量小于 25 时才能访问路由，否则，我们会将用户重定向到首页。

打开这个中间件：

`app/Http/Middleware/YoungMiddleware.php`

```php
<?php

namespace App\Http\Middleware;

use Closure;

class YoungMiddleware
{
    /**
     * Handle an incoming request.
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        return $next($request);
    }
}
```

默认生成的中间件代码是没做任何过滤的，也就是说任何请求都会通过。

下面我们加入我们的过滤代码：

```php
<?php

namespace App\Http\Middleware;

use Closure;

class YoungMiddleware
{
    /**
     * Handle an incoming request.
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        // 如果传入的参数 age 大于25，重定向到'/'
        if ($request->age > 25) {
            return redirect('/');
        }
        return $next($request);
    }
}
```

至此，中间件创建完成。

## 三、注册并添加中间件

中间件创建好后，还需要`注册`并添加到相应的路由上，才能工作。

注册中间件：

打开 `app/Http/Kernel.php`

```php
<?php

namespace App\Http;

use Illuminate\Foundation\Http\Kernel as HttpKernel;

class Kernel extends HttpKernel
{
    /**
     * The application's global HTTP middleware stack.
     * @var array
     */
    protected $middleware = [
        \Illuminate\Foundation\Http\Middleware\CheckForMaintenanceMode::class,
        \App\Http\Middleware\EncryptCookies::class,
        \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
        \Illuminate\Session\Middleware\StartSession::class,
        \Illuminate\View\Middleware\ShareErrorsFromSession::class,
        \App\Http\Middleware\VerifyCsrfToken::class,
    ];

    /**
     * The application's route middleware.
     * @var array
     */
    protected $routeMiddleware = [
        'auth' => \App\Http\Middleware\Authenticate::class,
        'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
        'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,

    ];
}
```

如果你希望`每个 HTTP 请求`都经过一个中间件，只要将该中间件的类加入到 `app/Http/Kernel.php` 的 `$middleware `属性清单列表中。

如果你要指派中间件给`特定路由`，就要将该中间件的类加入到 app/Http/Kernel.php 的 `$routeMiddleware `属性清单列表中，并给这个中间件起一个名字。

因为我们这里只需要对我们本节实验开始创建的路由添加这个中间件，所以我们修改代码如下：

`app/Http/Kernel.php`

```php
<?php

namespace App\Http;

use Illuminate\Foundation\Http\Kernel as HttpKernel;

class Kernel extends HttpKernel
{
    /**
     * The application's global HTTP middleware stack.
     * @var array
     */
    protected $middleware = [
        \Illuminate\Foundation\Http\Middleware\CheckForMaintenanceMode::class,
        \App\Http\Middleware\EncryptCookies::class,
        \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
        \Illuminate\Session\Middleware\StartSession::class,
        \Illuminate\View\Middleware\ShareErrorsFromSession::class,
        \App\Http\Middleware\VerifyCsrfToken::class,

        //如果需要全局中间件在这里注册
    ];

    /**
     * The application's route middleware.
     * @var array
     */
    protected $routeMiddleware = [
        'auth' => \App\Http\Middleware\Authenticate::class,
        'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
        'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
        //在这里添加了一行
        'young' => \App\Http\Middleware\YoungMiddleware::class,
    ];
}
```

注册完成后，我们回到`routes.php`文件中为路由添加中间件。

打开 `app/Http/routes.php` 修改代码：

app/Http/routes.php

```php
Route::get('/young/{age}', 'UserController@young')->middleware('young');
```

至此，一个简单的中间件就完成了！

此时，根据我们之前的思路：

- age > 25 的路由如 `网址/young/56` 应该会被重定向到 `'/'`
- age <= 25 的路由如 `网址/young/18` 应该会通过中间件，到达控制器`UserController@young`

最后，完成控制器部分的代码。

使用 artisan 命令创建一个控制器：

```shell
php artisan make:controller UserController  --plain
```

> 添加 --plain 后缀可以不生成默认的空方法。

打开`app/Http/Controllers/UserController.php`，添加`young()`方法：

app/Http/Controllers/UserController.php

```php
class UserController extends Controller
{
    public function young(){
        return 'I`m young!';
    }
}
```

至此，所有的代码都完成了，打开浏览器测试一下效果。

访问 `localhost/young/18` 成功显示，如下图：I m young!



访问 `localhost/young/56` 重定向到了 `'/'` 如下图：[空白]

# 视图

## 一、什么是视图？

视图包含你应用程序所用到的 HTML，它能够将控制器和应用程序逻辑在呈现逻辑中进行分离。

简单的来说视图就是我们的网站能看到的一个一个的界面，在前面的实验中，为了简单，我们在处理完路由请求后返回的都是一些简单的字符，并没有使用真正的视图。

从本次实验开始，我们将加入视图使我们的网站更美观：）

## 二、视图基础

视图文件存放在 `resources/views` 目录下，还记得刚安装完 laravel 的时候的欢迎页吗，这就是一个视图文件。

我们打开 `resources/views/welcome.blade.php` 这个文件:

```html
<!DOCTYPE html>
<html>
    <head>
        <title>Laravel</title>

        <link href="https://fonts.googleapis.com/css?family=Lato:100" rel="stylesheet" type="text/css">

        <style>
            html, body {
                height: 100%;
            }

            body {
                margin: 0;
                padding: 0;
                width: 100%;
                display: table;
                font-weight: 100;
                font-family: 'Lato';
            }

            .container {
                text-align: center;
                display: table-cell;
                vertical-align: middle;
            }

            .content {
                text-align: center;
                display: inline-block;
            }

            .title {
                font-size: 96px;
            }
        </style>
    </head>
    <body>
        <div class="container">
            <div class="content">
                <div class="title">Hello World</div>
            </div>
        </div>
    </body>
</html>
```

如果你有过前端开发经验的话，这个界面你一定会很熟悉，视图文件就是主要以 HTML 组成的。

此时我们访问 `localhost/welcome` 就可以看到这个页面: Hello world

打开 `routes.php` 文件:

`app/Http/routes.php`

```php
<?php
Route::get('welcome', function () {
    return view('welcome');
});
```

我们可以像这样使用全局的辅助函数 view 来返回一个视图:

```php
Route::get('/', function ()    {
    return view('greeting', ['name' => 'James']);
});
```

`view()` 辅助函数的第一个参数会对应到 resources/views 目录内视图文件的名称。

`view()` 辅助函数的第二个参数是一个能够在视图内取用的数据数组。

视图文件也可以保存在 `resources/views` 目录下的子文件夹内，比如我们打开 `resources/views` 这个目录可以看到有一个 `errors` 文件夹，里边有一个 `503.blade.php` 文件 这个文件是 laravel 为我们生成的一个用来显示错误的页面，那么如何显示这个视图呢？

打开 `routes.php` 文件，添加以下代码:

app/Http/routes.php

```php
<?php
Route::get('errors', function () {
    return view('errors.503');
});
```

然后访问 `localhost/errors` 就可以看到这个视图了。Be right back

然后我们再尝试给视图文件传一个参数,打开 `routes.php` 文件，修改代码：

app/Http/routes.php

```php
<?php
Route::get('errors', function () {
    return view('errors.503', ['message' => '503 ERROR']);
});
```

相应的，打开 `resources/views/errors/503.blade.php`，修改代码：

resources/views/errors/503.blade.php

```php
<!DOCTYPE html>
<html>
.
.
.
    <body>
        <div class="container">
            <div class="content">
                <div class="title">{{ $message }}</div>
            </div>
        </div>
    </body>
</html>
```

> 其中 {{ $message }} 是 blade 模板语法，意思是输出 $message 这个变量，下次实验会专门学习 blade 模板引擎。

然后访问 `localhost/errors` 可以看到参数成功被传到了视图：503 ERRORS

## 三、创建一个视图

下面我们为首页创建一个最简单的视图，并使用控制器。

### 1.首先，打开 `routes.php` 修改首页路由：

app/Http/routes.php

```php
//首页
Route::get('/', 'HomeController@home')->name('home');
```

使用 `artisan` 创建控制器：

```shell
cd ~/Code/myweb
php artisan make:controller HomeController --plain
```

打开 `app/Http/Controllers/HomeController.php` 创建 home 方法：

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

use App\Http\Requests;
use App\Http\Controllers\Controller;

class HomeController extends Controller
{
    public function home()
    {
        return view('home');
    }
}
```

### 2.创建视图：

在 `resources/views` 目录下创建一个文件

命名为 `home.blade.php`

这样命名是因为我们要使用 laravel 的 blade 模板引擎来渲染我们的视图，blade 模板会在下一次实验讲到

然后打开 `resources/views/home.blade.php` 文件，添加代码：

resources/views/home.blade.php

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Home</title>
  </head>
  <body>
    <h1>Home</h1>
    <p>Welcome to My web</p>
  </body>
</html>
```

访问 `localhost` 就可以看到我们创建的视图了。

# Blade模版

本次实验我们学习 blade 模板，blade 模板可以让你在视图代码中实现更加强大的功能。

## 一、什么是Blade模版？

Blade 是 Laravel 提供的一个既简单又强大的模板引擎。

和其他流行的 PHP 模板引擎不一样，Blade 并不限制你在视图中使用原生 PHP 代码。所有 Blade 视图文件都将被编译成原生的 PHP 代码并缓存起来，除非它被修改，否则不会重新编译，这就意味着 Blade 基本上不会给你的应用增加任何额外负担。

Blade 视图文件使用 .blade.php 扩展名，一般被存放在 resources/views 目录。

像之前实验中的 welcome.blade.php 和 home.blade.php 等都是 blade 模板文件，只是我们之前还没有用到模板的功能，在本次实验我们将体验强大的 blade 模板功能。

## 二、模板继承

模板继承是最常用的一个 blade 模板功能。

平时访问网站的时候，可以发现，一般一个网站的不同页面都应该是类似的，比如都有相同的导航栏和底部信息栏，或者都有相同的左侧菜单栏。

我们在写代码的时候不可能每个页面都要把重复的东西写一遍，这样不仅效率低下，还不易维护，所以就有了模板继承。

下面我们通过一个很简单的例子来使用模板继承。

首先，创建相关路由：

app/Http/routes.php

```php
<?php
//home page
Route::get('/', 'StaticPagesController@home')->name('home');

//about page
Route::get('/about', 'StaticPagesController@about')->name('about');
```

然后，使用 artisan 创建控制器：

```shell
cd ~/Code/myweb
php artisan make:controller StaticPagesController --plain
```

打开控制器，创建相应方法：

app/Http/Controllers/StaticPagesController.php

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

use App\Http\Requests;
use App\Http\Controllers\Controller;

class StaticPagesController extends Controller
{
    public function home()
    {
        return view('home');
    }

    public function about()
    {
        return view('about');
    }
}
```

然后，在 `resources/views` 目录下创建对应的视图文件 `home.blade.php` 和 `about.blade.php`。

打开这两个视图文件，分别输入以下代码：

resources/views/home.blade.php

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Home</title>
  </head>
  <body>
    <h1>Home</h1>
    <p>Welcome to My web</p>
  </body>
</html>
```

resources/views/about.blade.php

```html
<!DOCTYPE html>
<html>
  <head>
    <title>About</title>
  </head>
  <body>
    <h1>About</h1>
    <p>This is my first Laravel web</p>
    <p>Author: SadCreeper</p>
    <!-- 可以更改为你自己的信息 -->
  </body>
</html>
```

然后，打开浏览器，输入 `localhost` 和 `localhost/about` 可以看到我们创建的两个视图。

但是到现在为止，我们还没有使用 blade 模板继承，可以看到两个视图中有很多的重复代码，下面创建一个基础视图，其他的所有视图都将继承这个基础视图。

在 `resources/views` 目录下新建一个目录 `layouts` 然后在 `resources/views/layouts` 目录下新建一个视图文件 `app.blade.php`。

打开这个 `app.blade.php` 加入如下代码：

resources/views/layouts/app.blade.php

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Myweb</title>
  </head>
  <body>    
    @yield('content')
  </body>
</html>
```

然后修改 `home.blade.php` 和 `about.blade.php`：

resources/views/home.blade.php

```php
@extends('layouts.app')

@section('content')
<h1>Home</h1>
<p>Welcome to My web</p>
@endsection
```

resources/views/about.blade.php

```php
@extends('layouts.app')

@section('content')
<h1>About</h1>
<p>This is my first Laravel web</p>
<p>Author: SadCreeper</p>
<!-- 可以更改为你自己的信息 -->
@endsection
```

其中顶部代码：

```php
@extends('layouts.app')
```

就是表示当前视图继承自 `layouts` 文件夹下的 `app.blade.php` 视图。

然后用下面这种方式就可以把 `section('content')` 中包含的内容放到被继承的视图 `app.blade.php` 中的 `yield('content')` 部分。

```php
@section('content')
//
@endsection
```

使用模板继承后，你如果想对网站进行一些基础的公用代码的修改时，就可以修改 `app.blade.php`。

下面我们给这个基础视图添加一个非常简单的顶部导航栏和底部信息栏。

resources/views/layouts/app.blade.php

```php+HTML
<!DOCTYPE html>
<html>
  <head>
    <title>Myweb</title>
  </head>
  <body style="margin:0;padding:0;">
      <!-- header -->   
      <div style="padding:10px 50px 10px 50px;border-bottom: 1px solid #eeeeee;">
          <div style="display:inline-block;">
              <a href="{{ route('home') }}"><h2>Myweb</h2></a>
          </div>

          <div style="display:inline-block;margin-left:20px;">
              <a href="{{ route('about') }}">about</a>
          </div>
      </div> 

      <div style="text-align:center;">
          @yield('content')
      </div>


    <!-- footer -->
      <div style="padding:10px 50px 10px 50px;background-color:gray;">
          <p>contact me : 1234567</p>
      </div> 
  </body>
</html>
```

> 在本教程中，我把样式写到了 `style` 里，是为了简化流程，通常来说，样式代码应该尽量写到 css 文件中，以方便复用和维护。

此时我们访问 `localhost` 和 `localhost/about` 都可以看到完整的包含导航栏和底部信息栏的视图。

## 二、引入子视图

你可以使用 Blade 的 @include 命令来引入一个已存在的视图，所有在父视图的可用变量在被引入的视图中都是可用的。

```php
<div>
    @include('shared.errors')

    <form>
        <!-- Form Contents -->
    </form>
</div>
```

尽管被引入的视图会继承父视图中的所有数据，你也可以通过传递额外的数组数据至被引入的页面：

```php
@include('view.name', ['some' => 'data'])
```

下面我们创建一个子视图，用来显示网站作者信息，你可以在网站上任何想显示作者信息的地方引用这个视图。

在 `resources/views` 目录下新建一个文件夹 `shared` 然后在 `resources/views/shared` 目录下新建一个文件 `author.blade.php` 打开该文件。

resources/views/shared/author.blade.php

```php
<div style="width:200px;margin:20px auto 20px auto;padding:20px;border:1px solid black;">
    <h3>Author</h3>
    <p>name : SadCreeper</p>
    <p>age : 22</p>
    <p>Tel : 150-XXXX-XXXX</p>
</div>
```

然后在 `resources/views/about.blade.php` 中引用该子视图：

resources/views/about.blade.php

```php
@extends('layouts.app')

@section('content')
<h1>About</h1>
<p>This is my first Laravel web</p>

@include('shared.author')

@endsection
```

然后访问`localhost/about` 看下效果：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2502timestamp1484542266530.png/wm)

此时如果你想在任何地方显示这个子视图，只需要在相应位置加入下面一行代码即可~

```php
@include('shared.author')
```

## 三、数据显示

你可以使用 「中括号」 包住变量以显示传递至 Blade 视图的数据。如假设你有下面这样一个路由：

```php
Route::get('greeting', function () {
    return view('welcome', ['name' => 'Samantha']);
});
```

你在视图文件中可以像这样显示 name 变量的内容：

```php
Hello, {{ $name }}.
```

你也可以显示 PHP 函数的结果。事实上，你可以在 Blade 中显示任意的 PHP 代码：

```php
The current UNIX timestamp is {{ time() }}.
```

有时候你可能想要输出一个变量，但是你并不确定这个变量是否已经被定义，我们可以这样：

```php
{{ $name or 'Default' }}
```

在这个例子中，如果 $name 变量存在，它的值将被显示出来。但是，如果它不存在，则会显示 Default 。

在默认情况下，Blade 模板中的 {{ }} 表达式将会自动调用 PHP htmlentities 函数来转义数据以避免 XSS 的攻击。如果你不想你的数据被转义，你可以使用下面的语法：

```php
Hello, {!! $name !!}.
```

## 四、控制输出

除了模板继承与数据显示的功能以外，Blade 也给一般的 PHP 结构控制语句提供了方便的缩写，比如条件表达式和循环语句。这些缩写提供了更为清晰简明的方式来使用 PHP 的控制结构，而且还保持与 PHP 语句的相似性。

### 1.IF

你可以通过 `@if` , `@elseif` , `@else` 及 `@endif` 指令构建 if 表达式，这些命令的功能等同于在 PHP 中的语法：

```php
@if (count($records) === 1)
    我有一条记录！
@elseif (count($records) > 1)
    我有多条记录！
@else
    我没有任何记录！
@endif
```

为了方便，Blade 也提供了一个 @unless 命令：

```php
@unless (Auth::check())
    你尚未登录。
@endunless
```

### 2.循环

```php
@for ($i = 0; $i < 10; $i++)
    目前的值为 {{ $i }}
@endfor

@foreach ($users as $user)
    <p>此用户为 {{ $user->id }}</p>
@endforeach

@forelse ($users as $user)
    <li>{{ $user->name }}</li>
@empty
    <p>没有用户</p>
@endforelse

@while (true)
    <p>我永远都在跑循环。</p>
@endwhile
```

当使用循环时，你可能也需要一些结束循环或者跳出当前循环的命令：

```php
@foreach ($users as $user)
    @if ($user->type == 1)
        @continue
    @endif

    <li>{{ $user->name }}</li>

    @if ($user->number == 5)
        @break
    @endif
@endforeach
```

或者也可以简写：

```php
@foreach ($users as $user)
    @continue($user->type == 1)

    <li>{{ $user->name }}</li>

    @break($user->number == 5)
@endforeach
```

当循环进行时，你可以使用 循环变量 `$loop` 来获取循环中有价值的信息，比如当前循环的索引，当前循环是不是首次迭代，又或者当前循环是不是最后一次迭代。

```php
@foreach ($users as $user)
    @if ($loop->first)
        This is the first iteration.
    @endif

    @if ($loop->last)
        This is the last iteration.
    @endif

    <p>This is user {{ $user->id }}</p>
@endforeach
```

如果你是在一个嵌套的循环中，你可以通过使用 loop 变量的 parent 属性来获取父循环中的*l**o**o**p*变量的*p**a**r**e**n**t*属性来获取父循环中的loop 变量：

```php
@foreach ($users as $user)
    @foreach ($user->posts as $post)
        @if ($loop->parent->first)
            This is first iteration of the parent loop.
        @endif
    @endforeach
@endforeach
```

$loop 变量也包含了其它各种有用的属性：

| 属性             | 描述                                                  |
| ---------------- | ----------------------------------------------------- |
| $loop->index     | 当前循环所迭代的索引，起始为 0。                      |
| $loop->iteration | 当前迭代数，起始为 1。                                |
| $loop->remaining | 循环中迭代剩余的数量。                                |
| $loop->count     | 被迭代项的总数量。                                    |
| $loop->first     | 当前迭代是否是循环中的首次迭代。                      |
| $loop->last      | 当前迭代是否是循环中的最后一次迭代。                  |
| $loop->depth     | 当前循环的嵌套深度。                                  |
| $loop->parent    | 当在嵌套的循环内时，可以访问到父循环中的 $loop 变量。 |

## 五、注释

Blade 也允许在页面中定义注释，然而，跟 HTML 的注释不同的是，Blade 注释不会被包含在应用程序返回的 HTML 内：

```php
{{-- 此注释将不会出现在渲染后的 HTML --}}
```

# 数据库 & 数据库迁移

在前面的实验中，我们还没有使用到数据库，在实际的开发中，网站上用到的动态数据基本都要保存在数据库中，所以如何高效快速地和数据库进行通信，如何高效快速地对数据库中的数据进行操作是非常重要的一个环节。

## 一、数据库简介

Laravel 对主流数据库系统连接和查询都提供了很好的支持，尤其是流畅的**查询语句构造器**。

Laravel 支持四种类型的数据库：

- MySQL
- Postgres
- SQLite
- SQL Server

> 本系列教程选用了 mysql

Laravel 应用程序的数据库配置文件放置在 config/database.php 文件中。

在这个配置文件内你可以定义所有的数据库连接，以及指定默认使用哪个连接。在此文件内提供了所有支持的数据库系统示例。

默认情况下，Laravel 的 环境配置 示例会使用 Laravel Homestead。

对于 Laravel 开发来说这是一个相当便利的本地虚拟机。当然你也可以根据需求来随时修改本机端的数据库设置。

## 二、数据库配置

如果你使用 homestead ，数据库应该在第二个实验就配置好了`（本地搭建开发环境的情况）`，如果你使用实验楼的在线环境，还需要手动配置一下数据库。

首先，启动 mysql 数据库，打开命令行，输入以下指令：

```shell
sudo service mysql start
```

然后登陆 mysql，用户名 `root` 密码为空。

```shell
mysql -u root -p
```

登陆后会进入 mysql 的命令行，如下：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2509timestamp1484545663118.png/wm)

在mysql的命令行中完成后续操作。

创建一个数据库，命名为`myweb`：

```mysql
create database myweb;
```

使用如下命令可以查看当前已经存在的数据库：

```mysql
show databases;
```

效果图如下：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2509timestamp1484545755049.png/wm)

然后按 `ctrl+c` 退出 mysql 命令行。

打开我们的工程代码，找到根目录下的 `.env` 文件。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2509timestamp1484545902939.png/wm)

修改代码如下：

.env

```php
.

DB_HOST=localhost
DB_DATABASE=myweb
DB_USERNAME=root
DB_PASSWORD=

.
```

到此，数据库环境配置完成！

## 三、数据库迁移

如果你使用过 Git 的话，你一定对代码的版本控制非常熟悉。

Laravel 中的数据库迁移就像是数据库的版本控制系统，他可以让你的团队轻松修改并共享应用程序的数据库结构，迁移通常会搭配上 Laravel 的数据库结构构造器来让你方便地构建数据库结构。

迁移文件存放在 `database/migrations` 文件夹内，Laravel 默认写好了两个迁移文件，我们可以看一下这两个文件。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2509timestamp1484547200740.png/wm)

看名字就可以知道，这两个迁移文件一个创建了一张 `users` 数据表，一个创建了一张 `password_resets` 数据表。

打开 `2014_10_12_000000_create_users_table.php` （前边的数字代表了创建时间）：

database/migrations/XXX_create_users_table.php

```php
<?php

use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateUsersTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->increments('id');
            $table->string('name');
            $table->string('email')->unique();
            $table->string('password', 60);
            $table->rememberToken();
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::drop('users');
    }
}
```

一个迁移类会包含两个方法：`up()` 和 `down()` ：

- `up()` 方法可为数据库添加新的数据表、字段或索引，
- `down()` 方法则是 up 方法的逆操作。

你可以在这两个方法中使用 Laravel 数据库结构构造器来创建以及修改数据表。

比如上面创建 `users` 的代码：

```php
Schema::create('users', function (Blueprint $table) {
            $table->increments('id'); //创建递增字段‘id’
            $table->string('name'); //创建字符串字段‘name’
            $table->string('email')->unique(); //创建唯一字符串字段‘email’
            $table->string('password', 60); //创建字符串字段‘password’ 最大字符数60
            $table->rememberToken(); //创建记住密码字段
            $table->timestamps(); //创建时间戳
        });
```

关于数据库结构构造器，可查阅[官方文档](https://laravel-china.org/docs/5.3/migrations#creating-tables)。

## 四、运行迁移

一旦你写好了迁移文件，就可以通过一行命令来运行迁移。

首先进入项目位置：

```shell
cd ~/Code/myweb
```

运行迁移：

```shell
php artisan migrate
```

可以看到输出了执行信息：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2509timestamp1484547964685.png/wm)

然后进入 mysql 查看一下数据库：

```shell
mysql -u root -p
```

进入 `myweb` 数据库：

```mysql
use myweb;
```

查看所有的数据表：

```mysql
show tables;
```

可以看到 `users` 数据表已经被创建了。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2509timestamp1484548320272.png/wm)

查看 `users` 表结构：

```mysql
desc users;
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2509timestamp1484548462730.png/wm)

可以看到 `users` 这张表的结构也已经严格按照我们的迁移文件设置好了，按 `ctrl+c` 退出 mysql 命令行。

## 五、迁移回滚

就像 Git 的回滚代码一样，Laravel 的数据迁移也可以回滚。

进入项目代码，执行回滚：

```shell
cd ~/Code/myweb
php artisan migrate:rollback
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2509timestamp1484548665253.png/wm)

提示回滚成功，所以现在 `users` 和 `password_resets` 表应该已经被删除了，我们进入 mysql 查看一下：

```shell
mysql -u root -p
use myweb;
show tables;
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2509timestamp1484548821127.png/wm)

可以看到，数据表被成功删除，按 `ctrl+c` 退出 mysql 命令行。

## 六、生成迁移

以 Laravel 默认为我们写好的迁移文件为例，我们也可以生成迁移。

在代码目录下使用 artisan 生成迁移：

```shell
php artisan make:migration create_articles_table
```

可以看到 `database/migrations` 目录下生成了对应的迁移文件：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2509timestamp1484549220037.png/wm)

修改后执行迁移就可以对数据库进行想要的操作了。

# 模型 & Eloquent

本次实验将学习 Laravel 的模型（Model）和 强大的数据交互 Eloquent。

## 一、Eloquent简介

Laravel 的 `Eloquent ORM` 提供了漂亮、简洁的 ActiveRecord 实现来和数据库进行交互。

每个数据库表都有一个对应的「模型」可用来跟数据表进行交互。你可以通过模型查询数据表内的数据，以及将记录添加到数据表中。

## 二、定义模型

模型文件默认放在 `app` 目录下，打开该目录，可以看到 Laravel 默认生成的用户模型 `user.php` ，打开这个文件。

user.php

```php
<?php

namespace App;

use Illuminate\Auth\Authenticatable;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Auth\Passwords\CanResetPassword;
use Illuminate\Contracts\Auth\Authenticatable as AuthenticatableContract;
use Illuminate\Contracts\Auth\CanResetPassword as CanResetPasswordContract;

class User extends Model implements AuthenticatableContract, CanResetPasswordContract
{
    use Authenticatable, CanResetPassword;
    /**
     * The database table used by the model.
     * @var string
     */
    protected $table = 'users';
    /**
     * The attributes that are mass assignable.
     * @var array
     */
    protected $fillable = ['name', 'email', 'password'];
    /**
     * The attributes excluded from the model's JSON form.
     * @var array
     */
    protected $hidden = ['password', 'remember_token'];
}
```

`protected $table = 'users';` 设置了数据表的名称。

`protected $fillable = ['name', 'email', 'password'];` 设置了可以更新的字段。

`protected $hidden = ['password', 'remember_token'];` 设置了哪些字段会被隐藏。

## 三、创建模型

我们也可以创建一个新的模型，例如使用 artisan 创建一个 Article 模型：

```shell
cd ~/Code/myweb
php artisan make:model Article
```

打开 `app` 目录即可看到我们创建的 Article 模型文件。

## 四、数据读取

一旦你创建并关联了一个模型到数据表上，那么你就可以从数据库中获取数据。

可以把每个 Eloquent 模型想像成强大的**查询构造器**，它让你可以流畅地查询与模型关联的数据表。

比如下面这段代码可以取出 `users` 数据表中的所有数据。

```php
<?php

use App\User;

$users = App\User::all();

foreach ($users as $user) {
    echo $user->name;
}
```

或者用下面这段代码来对数据进行筛选，来选出 `users` 表中 `active` 字段为 1，的数据并且按照 `age` 倒序排列，然后取出前十条。

```php
$users = App\User::where('active', 1)
               ->orderBy('age', 'desc')
               ->take(10)
               ->get();
```

当然了，你也可以通过 `find()` 和 `first()` 方法来取回单条记录。

```php
// 通过主键取回一个模型...
$flight = App\Flight::find(1);

// 取回符合查询限制的第一个模型 ...
$flight = App\Flight::where('active', 1)->first();
```

你也可以用主键的集合为参数调用 find 方法，它将返回符合条件的集合：

```php
$flights = App\Flight::find([1, 2, 3]);
```

有时你可能希望在找不到数据时抛出一个异常，这在路由或是控制器内特别有用。

`findOrFail()` 以及 `firstOrFail()` 方法会取回查询的第一个结果。如果没有找到相应结果，则会抛出一个异常：

```php
$model = App\Flight::findOrFail(1);

$model = App\Flight::where('legs', '>', 100)->firstOrFail();
```

## 五、数据添加

要在数据库中创建一条新记录，只需创建一个新模型实例，并在模型上设置属性和调用 save 方法即可：

```php
<?php

namespace App\Http\Controllers;

use App\User;
use Illuminate\Http\Request;
use App\Http\Controllers\Controller;

class FlightController extends Controller
{
    /**
     * 创建一个新的用户实例。
     *
     * @param  Request  $request
     * @return Response
     */
    public function store(Request $request)
    {
        // 验证请求...

        $user = new User;

        $user->name = $request->name;

        $user->save();
    }
}
```

在这个例子中，我们把来自 HTTP 请求中的 `name` 参数简单地指定给 `App\User` 模型实例的 `name` 属性。

当我们调用 `save()` 方法，就会添加一条记录到数据库中。

当 `save()` 方法被调用时，`created_at` 以及 `updated_at` 时间戳将会被自动设置，因此我们不需要去手动设置它们。

## 六、数据更新

`save()` 方法也可以用于更新数据库中已经存在的模型。

要更新模型，则须先取回模型，再设置任何你希望更新的属性，接着调用 `save()` 方法就可以了。

```php
$user = App\User::find(1);

$user->name = 'New User Name';

$user->save();
```

## 七、数据删除

在模型实例上调用 `delete()` 方法来删除数据：

```php
$user = App\User::find(1);

$user->delete();
```

在上面的例子中，我们在调用 delete 方法之前会先从数据库中取回了这个模型。

我们也可以不用取回直接删除它

- 直接删除，使用 `destroy()` 方法：

```php
App\User::destroy(1);

App\User::destroy([1, 2, 3]);

App\User::destroy(1, 2, 3);
```

- 通过查询来删除模型

当然了，你也可以删除某些满足条件的数据，例如我们删除所有被标为不活跃的用户：

```php
$deletedRows = App\User::where('active', 0)->delete();
```

## 八、Tinker

通过上述知识的学习，你应该对 Eloquent ORM 模型 有了一定的了解，但是要想熟练掌握，总要实际操作一下才行。

Laravel 内置了一个小工具可以用来快速调试数据库的数据 `php artisan tinker`。

Laravel artisan 的 tinker 是一个 REPL (read-eval-print-loop)，REPL 是指 交互式命令行界面，它可以让你输入一段代码去执行，并把执行结果直接打印到命令行界面里。

接下来我们就使用 Tinker 来实际联系一下数据操作：

### 1.首先要配置一下数据库

`（保存环境的同学可跳过这一步）`根据上次实验学过的知识快速完成配置

```shell
sudo service mysql start
mysql -u root -p
create database myweb;
```

.env文件

```php
.
.
.
DB_HOST=localhost
DB_DATABASE=myweb
DB_USERNAME=root
DB_PASSWORD=
.
.
.
```

### 2.然后执行迁移生成数据表

```shell
cd ~/Code/myweb
php artisan migrate
```

### 3.使用 artisan 打开 tinker

```shell
php artisan tinker
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2510timestamp1484556043842.png/wm)

### 4.添加一个用户

```php
$user = new App\User;
$user -> name = 'SadCreeper';
$user -> email = '12345@qq.com';
$user -> password = 'password';
$user -> save();
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2510timestamp1484556464166.png/wm)

### 5.查看用户信息

```php
App\User::find(1);
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2510timestamp1484556553310.png/wm)

### 6.更新用户信息

```php
$user = App\User::find(1);

$user->name = 'HappyCreeper';

$user->save();
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2510timestamp1484557177845.png/wm)

### 7.删除用户信息

```php
App\User::destroy(1);
App\User::find(1);
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2510timestamp1484557268266.png/wm)

# 30分钟搭建迷你博客

经过前面一系列的基础教程，相信你对 Laravel 开发已经不像开始那么陌生了。 在本章，我们通过搭建一个很简单的迷你博客把前面的知识再走一遍， 只有通过一次又一次的实战才能达到炉火纯青写起代码手上带风的效果：）

## 一、学习提醒

因为本实验篇幅较长，为防止发生大家做实验做到一半下次又要重新来的悲剧。

建议非会员用户可以使用实验楼提供的 Git 仓库来储存代码（[点此查看使用方法](https://www.shiyanlou.com/questions/360)），或者直接升级为会员即可直接保存环境。

## 二、设计与思路

在开始写第一行代码之前，一定要尽量从头到尾将我们要做的产品设计好，避免写完又改，多写不必要的代码。

- 需求分析：我们的迷你博客应该至少包含：新增/编辑/查看/删除文章，以及文章列表展示功能。
- 数据库分析：基于这个功能，我们只需要一张 Articles 数据表来存放文章即可。
- 页面结构分析：应该使用模板继承建立一张基础模板包含：导航栏/底部信息栏/作者信息等。

最后我们可以简单画个草图：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2512timestamp1484619614543.png/wm)

## 三、创建路由

完成这个博客大概需要以下几条路由：

| 路由             | 功能                             |
| ---------------- | -------------------------------- |
| 文章列表页面路由 | 返回文章列表页面                 |
| 新增文章页面路由 | 返回新增文章页面                 |
| 文章保存功能路由 | 将文章保存到数据库               |
| 查看文章页面路由 | 返回文章详情页面                 |
| 编辑文章页面路由 | 返回编辑文章页面                 |
| 编辑文章功能路由 | 将文章取出更新后重新保存到数据库 |
| 删除文章功能路由 | 将文章从数据库删除               |

可以看到几乎全部是对文章的数据操作路由，针对这种情况，Laravel 提供了非常方便的办法：RESTful 资源控制器和路由。

打开`routes.php`加入如下代码：

```
Route::resource('articles', 'ArticlesController');
```

只需要上面这样一行代码，就相当于创建了如下7条路由，且都是命名路由，我们可以使用类似`route('articles.show')` 这样的用法。

```
Route::get('/articles', 'ArticlesController@index')->name('articles.index');
Route::get('/articles/{id}', 'ArticlesController@show')->name('articles.show');
Route::get('/articles/create', 'ArticlesController@create')->name('articles.create');
Route::post('/articles', 'ArticlesController@store')->name('articles.store');
Route::get('/articles/{id}/edit', 'ArticlesController@edit')->name('articles.edit');
Route::patch('/articles/{id}', 'ArticlesController@update')->name('articles.update');
Route::delete('/articles/{id}', 'ArticlesController@destroy')->name('articles.destroy');
```

app/Http/routes.php

```
<?php

/*
|--------------------------------------------------------------------------
| Application Routes
|--------------------------------------------------------------------------
|
| Here is where you can register all of the routes for an application.
| It's a breeze. Simply tell Laravel the URIs it should respond to
| and give it the controller to call when that URI is requested.
|
*/

Route::get('/', function () {
    return view('welcome');
});

Route::resource('articles', 'ArticlesController');
```

## 四、创建控制器

利用 artisan 创建一个文章控制器：

```
php artisan make:controller ArticlesController
```

## 五、创建基础视图

在 `resources/views` 目录下新建一个文件夹 `layouts`， 然后在 `resources/views/layouts` 目录下新建一个文件 `app.blade.php`。

打开 `app.blade.php`：

resources/views/layouts/app.blade.php

```
<!DOCTYPE html>
<html>
  <head>
    <title>Myweb</title>

    <style type="text/css">
        body{margin: 0;padding: 0;background-color: #DEDEDE;}
        a{text-decoration:none;}
        .header{padding:10px 50px 10px 50px;border-bottom: 1px solid #eeeeee;}
        .header>.logo{display:inline-block;}
        .header>.menu{display:inline-block;margin-left:20px;}
        .content{}
        .left{background-color: white;margin: 25px 300px 25px 25px;padding: 25px;box-shadow: 1px 1px 2px 1px #848484;}
        .right{background-color: white;width: 200px;margin: 25px;padding: 25px;box-shadow: 1px 1px 2px 1px #848484;position: absolute;top: 92px;right: 0;}
        .footer{padding:10px 50px 10px 50px;background-color:gray;}
    </style>
  </head>

  <body>
      <!-- header -->   
      <div class="header">
          <div class="logo">
              <a href="#"><h2>Myweb</h2></a>
          </div>

          <div class="menu">
              <a href="{{ route('articles.index') }}">Articles</a>
          </div>
      </div> 

      <div class="content">
          <div class="left">
              @yield('content')
          </div>

          <div class="right">

          </div>    
      </div>


    <!-- footer -->
      <div class="footer">
          <p>contact me : 1234567</p>
      </div> 
  </body>
</html>
```

接下来的所有视图我们都将继承这个视图，我们可以让控制器先返回基础视图看一下效果。

打开 `ArticlesController.php`：

app/Http/Controllers/ArticlesController.php

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

use App\Http\Requests;
use App\Http\Controllers\Controller;

class ArticlesController extends Controller
{
    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        return view('layouts.app');
    }

    .
    .
    .
```

然后访问 `localhost/articles` 就可以看到基础视图了。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2512timestamp1484637480670.png/wm)

## 六、新建文章表单

我们首先创建一个 文章列表展示页，作为我们的迷你博客首页。

在 `resources/views` 目录下新建一个文件夹 `articles` ，然后在 `resources/views/articles` 目录下新建一个文件 `index.blade.php`。

打开 `index.blade.php`：

resources/views/articles/index.blade.php

```
@extends('layouts.app')

@section('content')
    <a href="{{ route('articles.create') }}" style="padding:5px;border:1px dashed gray;">
        + New Article
    </a>
@endsection
```

然后打开 `ArticlesController.php` 将刚才的index函数修改为正确的视图：

app/Http/Controllers/ArticlesController.php

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

use App\Http\Requests;
use App\Http\Controllers\Controller;

class ArticlesController extends Controller
{
    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        return view('articles.index');
    }

    .
    .
    .
```

然后访问 `localhost/articles` 页面可以看到我们的文章列表详情页。

当然了现在还没有任何文章，但是我们创建了一个“新建文章”的按钮，用来新建文章，点击后会跳转到新建文章表单页面，注意这里的跳转，使用了命名路由。

```
<a href="{{ route('articles.create') }}" style="padding:5px;border:1px dashed gray;">
    + New Article
</a>
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2512timestamp1484641028548.png/wm)

如果这时你点击新建文章，你会发现跳到了空白页，因为现在其实是跳到了 `ArticlesController` 的 `create()` 方法里，而这个方法现在是空白的，所以就出现了空白页，我们打开`ArticlesController.php` 来补全这个方法。

app/Http/Controllers/ArticlesController.php

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

use App\Http\Requests;
use App\Http\Controllers\Controller;

class ArticlesController extends Controller
{
    .
    .
    .
    /**
     * Show the form for creating a new resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function create()
    {
        return view('articles.create');
    }
```

相应的，在`resources/views/articles` 目录下创建一个视图`create.blade.php`。

resources/views/articles/create.blade.php

```
@extends('layouts.app')

@section('content')
    <form action="{{ route('articles.store') }}" method="post">
        {{ csrf_field() }}
        <label>Title</label>
        <input type="text" name="title" style="width:100%;" value="{{ old('title') }}">
        <label>Content</label>
        <textarea name="content" rows="10" style="width:100%;">{{ old('content') }}</textarea>

        <button type="submit">OK</button>
    </form>
@endsection
```

> 注意上面代码中的 `{{ csrf_field() }}` ，这是 Laravel 的 CSRF 保护机制，你应该为你的每个表单添加这样一行代码，以使你的网站不受到跨网站请求伪造 攻击，详情参考[官方文档](https://laravel-china.org/docs/5.1/routing#csrf-protection)。

此时我们访问`localhost/articles` 点击新建文章就可以看到新建文章的表单了。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2512timestamp1484640982255.png/wm)

## 七、文章存储

此时如果你填写新建文章表单点击提交也会跳到一个空白页面，同样的道理，因为我们后续的控制器代码还没写。

要实现文章存储，首先要配置数据库，创建数据表，创建模型，然后再完成存储逻辑代码。

### 1.配置数据库

`（保存环境的同学可跳过这一步）`根据上次实验学过的知识快速完成配置：

```
sudo service mysql start
mysql -u root -p
create database myweb;
```

.env文件

```
.
.
.
DB_HOST=localhost
DB_DATABASE=myweb
DB_USERNAME=root
DB_PASSWORD=
.
.
.
```

### 2.创建数据表

利用 artisan 命令生成迁移：

```
php artisan make:migration create_articles_table
```

然后打开迁移文件：

database/migrations/XXXX_creat_articles_table.php

```
<?php

use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;

class CreateArticlesTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('articles', function (Blueprint $table) {
            $table->increments('id');
            $table->string('title');
            $table->longText('content')->nullable();
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('articles');
    }
}
```

我们创建了一张 `articles` 表，包含递增的 `id` 字段，字符串`title`字段，长文本`content`字段，和时间戳。

执行数据库迁移：

```
php artisan migrate
```

出现以下提示，创建成功！

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2512timestamp1484641715030.png/wm)

### 3.创建模型

利用 artisan 命令创建模型：

```
php artisan make:model Article
```

打开模型文件，输入以下代码：

app/Article.php

```
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Article extends Model
{
    protected $table = 'articles';

    protected $fillable = [
        'title', 'content',
    ];
}
```

指定了数据表的名称和可写字段。

### 4.存储逻辑代码

打开 `ArticlesController.php` 控制器，找到 `store()` 方法。

app/Http/Controllers/ArticlesController.php

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

use App\Http\Requests;
use App\Http\Controllers\Controller;

use App\Article;//  <---------注意这里

class ArticlesController extends Controller
{
    .
    .
    .

    /**
     * Store a newly created resource in storage.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return \Illuminate\Http\Response
     */
    public function store(Request $request)
    {
        $this->validate($request, [
            'title' => 'required|max:50',
        ]);

        $article = Article::create([
            'title' => $request->title,
            'content' => $request->content,
        ]);

        return redirect()->route('articles.index');
    }
```

因为这段代码中用到了Eloquent 模型的构造器用法，所以需要引入我们之前创建的模型。

```
use App\Article;
```

在收到表单传过来的数据时，可以先对表单数据进行验证：

```
$this->validate($request, [
    'title' => 'required|max:50',
]);
```

最后完成文章的创建，然后重定向到文章列表页：

```
$article = Article::create([
    'title' => $request->title,
    'content' => $request->content,
]);

return redirect()->route('article.index');
```

至此，新增文章功能就完成了，打开浏览器，访问 `localhost/articles` 点击新建文章，完成表单，点击添加。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2512timestamp1484642986566.png/wm)

添加后如果跳到了文章列表页，就说明成功了！

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2512timestamp1484643050577.png/wm)

但是此时，我们并不知道是不是真的添加成功了，我们可以去数据库里看一下是否有了刚刚插入的数据：

```
mysql -u root -p
use myweb;
select * from articles;
```

可以看到我们刚刚创建的一篇文章已经被成功的写入了数据库。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2512timestamp1484643264229.png/wm)

## 八、文章列表展示

完成了添加文章功能后，就可以实现我们的文章列表展示页了。

打开 `ArticlesController.php` 找到 `index()` 方法，添加代码如下：

app/Http/Controllers/ArticlesController.php

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

use App\Http\Requests;
use App\Http\Controllers\Controller;

use App\Article;

class ArticlesController extends Controller
{
    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        $articles = Article::orderBy('created_at','desc')->get();

        return view('articles.index', compact('articles'));
    }
    .
    .
    .
```

取出了全部的文章并根据 `created_at` （创建时间）倒序排列，然后传给视图。

现在我们就可以在视图文件中读取全部的文章并显示了。

转到文章列表展示页：

resoureces/views/articles/index.blade.php

```
@extends('layouts.app')

@section('content')
    <a href="{{ route('articles.create') }}" style="padding:5px;border:1px dashed gray;">
        + New Article
    </a>

    @foreach ($articles as $article)
    <div style="border:1px solid gray;margin-top:20px;padding:20px;">
        <h2>{{ $article->title }}</h2>
        <p>{{ $article->content }}</p>
    </div>
    @endforeach
@endsection
```

添加了一段循环输出的代码。

现在访问`localhost/articles`，可以看到文章正确的显示了出来。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2512timestamp1484714534687.png/wm)

现在点击新建，尝试新建一篇文章~

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2512timestamp1484714597527.png/wm)

## 九、编辑文章表单

编辑文章表单其实和之前创建的新建文章表单很类似，只是需要额外将现有的数据读取出来填在表单上。

首先我们在文章列表页的每个文章上添加一个编辑按钮：

resoureces/views/articles/index.blade.php

```
@extends('layouts.app')

@section('content')
    <a href="{{ route('articles.create') }}" style="padding:5px;border:1px dashed gray;">
        + New Article
    </a>

    @foreach ($articles as $article)
    <div style="border:1px solid gray;margin-top:20px;padding:20px;">
        <h2>{{ $article->title }}</h2>
        <p>{{ $article->content }}</p>
        <a href="{{ route('articles.edit', $article->id ) }}">Edit</a>
    </div>
    @endforeach
@endsection
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2512timestamp1484715326336.png/wm)

然后打开 `ArticlesController.php` 添加相应的逻辑代码：

app/Http/Controllers/ArticlesController.php

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

use App\Http\Requests;
use App\Http\Controllers\Controller;

use App\Article;

class ArticlesController extends Controller
{

    .
    .
    .
    /**
     * Show the form for editing the specified resource.
     *
     * @param  int  $id
     * @return \Illuminate\Http\Response
     */
    public function edit($id)
    {
        $article = Article::findOrFail($id);
        return view('articles.edit', compact('article'));
    }
    .
    .
    .
```

然后创建对应视图文件：

在 `resources/views/articles` 目录下新建一个文件 `edit.blade.php` 。

打开 `edit.blade.php`：

resources/views/articles/edit.blade.php

```
@extends('layouts.app')

@section('content')
    <form action="{{ route('articles.update', $article->id) }}" method="post">
        {{ csrf_field() }}
        {{ method_field('PATCH') }}
        <label>Title</label>
        <input type="text" name="title" style="width:100%;" value="{{ $article->title }}">
        <label>Content</label>
        <textarea name="content" rows="10" style="width:100%;">{{ $article->content }}</textarea>

        <button type="submit">OK</button>
    </form>
@endsection
```

> 注意这段代码中的 `{{ method_field('PATCH') }}`，这是跨站方法伪造，HTML 表单没有支持 PUT、PATCH 或 DELETE 动作。所以在从 HTML 表单中调用被定义的 PUT、PATCH 或 DELETE 路由时，你将需要在表单中增加隐藏的 _method 字段来伪造该方法，详情参考 [官方文档](https://laravel-china.org/docs/5.1/routing#form-method-spoofing)。

这个表单将会把数据传到 `update()` 方法中，所以接下来我们补全 `update()` 方法。

app/Http/Controllers/ArticlesController.php

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

use App\Http\Requests;
use App\Http\Controllers\Controller;

use App\Article;

class ArticlesController extends Controller
{

    .
    .
    .
    /**
     * Update the specified resource in storage.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  int  $id
     * @return \Illuminate\Http\Response
     */
    public function update(Request $request, $id)
    {
        $this->validate($request, [
            'title' => 'required|max:50',
        ]);

        $article = Article::findOrFail($id);
        $article->update([
            'title' => $request->title,
            'content' => $request->content,
        ]);

        return back();
    }
    .
    .
    .
```

到此，编辑文章功能就完成了，点击编辑后将出现编辑表单，输入新的内容后点击 “OK” 就可以成功更新数据。

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2512timestamp1484716165918.png/wm)

## 十、删除文章

首先我们在文章列表页的每个文章上添加一个删除按钮：

resoureces/views/articles/index.blade.php

```
@extends('layouts.app')

@section('content')
    <a href="{{ route('articles.create') }}" style="padding:5px;border:1px dashed gray;">
        + New Article
    </a>

    @foreach ($articles as $article)
    <div style="border:1px solid gray;margin-top:20px;padding:20px;">
        <h2>{{ $article->title }}</h2>
        <p>{{ $article->content }}</p>
        <a href="{{ route('articles.edit', $article->id ) }}">Edit</a>
        <form action="{{ route('articles.destroy', $article->id) }}" method="post" style="display: inline-block;">
            {{ csrf_field() }}
            {{ method_field('DELETE') }}
            <button type="submit" style="color: #F08080;background-color: transparent;border: none;">Delete</button>
        </form>
    </div>
    @endforeach
@endsection
```

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2512timestamp1484716504818.png/wm)

然后打开 `ArticlesController.php` 添加相应的逻辑代码：

app/Http/Controllers/ArticlesController.php

```
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

use App\Http\Requests;
use App\Http\Controllers\Controller;

use App\Article;

class ArticlesController extends Controller
{

    .
    .
    .
    /**
     * Remove the specified resource from storage.
     *
     * @param  int  $id
     * @return \Illuminate\Http\Response
     */
    public function destroy($id)
    {
        $article = Article::findOrFail($id);
        $article->delete();
        return back();
    }
```

删除功能这样就做好了，现在访问文章列表展示页 `localhost/articles` 点击删除按钮，可以看到文章被删除。

## 十一、作者信息

最后，让我们添加作者信息。

打开基础视图：

resources/views/layouts/app.blade.php

```
.
.
.      
<!-- header -->   
<div class="header">
  <div class="logo">
      <a href="#"><h2>Myweb</h2></a>
  </div>

  <div class="menu">
      <a href="{{ route('articles.index') }}">Articles</a>
  </div>
</div> 

<div class="content">
  <div class="left">
      @yield('content')
  </div>

  <div class="right">
    <!-- 这里 -->
    <div style="padding:20px;border:1px solid black;">
      <h3>Author</h3>
      <p>name : SadCreeper</p>
      <p>age : 22</p>
      <p>Tel : 150-XXXX-XXXX</p>
    </div>
    <!-- 这里 -->
  </div>    
</div>
```

全部完成了！

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2512timestamp1484717151888.png/wm)

# 使用 bootstrap 美化样式

## 开始

在前面的11章教程中，我们并没有使用 bootstrap 这也是处于对降低门槛方面的考虑，事实上，Laravel 已经默认集成了 bootstrap 框架，我们很容易就能使用它

bootstrap 是世界上使用最广泛的前端框架之一，它提供了一套简介、精美的UI组件，几乎涵盖了网站上常用的所有功能，如果你的应用对样式的要求不是特别高，使用 bootstrap 将是最好的选择

## 基础知识介绍

### bootstrap

**百度百科对 bootstrap 的介绍**

Bootstrap，来自 Twitter，是目前很受欢迎的前端框架。

Bootstrap 是基于 HTML、CSS、JAVASCRIPT 的，它简洁灵活，使得 Web 开发更加快捷。

它由Twitter的设计师Mark Otto和Jacob Thornton合作开发，是一个CSS/HTML框架。

Bootstrap提供了优雅的HTML和CSS规范，它即是由动态CSS语言Less写成。Bootstrap一经推出后颇受欢迎，一直是GitHub上的热门开源项目，包括NASA的MSNBC（微软全国广播公司）的Breaking News都使用了该项目。国内一些移动开发者较为熟悉的框架，如WeX5前端开源框架等，也是基于Bootstrap源码进行性能优化而来。

**bootstrap 中文网**

[http://www.bootcss.com](http://www.bootcss.com/)

**一些 bootstrap 样式截图**

按钮组件：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2515timestamp1486964000269.png/wm)

导航栏：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2515timestamp1486964069971.png/wm)

下拉菜单：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2515timestamp1486964135477.png/wm)

**一般来说，bootstrap 是给后端开发人员用的**，因为使用 bootstrap 你可以快速搭建一套网站前端页面，并且样式看起来也过得去

但是如果你对前端开发感兴趣，也可以从学习 bootstarp 入手，毕竟 bootstrap 中凝结了很多前辈的经验，有很多值得学习借鉴之处

### npm

**百度百科对 npm 的介绍**

NPM的全称是Node Package Manager ，是一个NodeJS包管理和分发工具，已经成为了非官方的发布Node模块（包）的标准。

如果你熟悉ruby的gem，Python的pypi、setuptools，PHP的pear，那么你就知道NPM的作用是什么了。

Nodejs自身提供了基本的模块，但是开发实际应用过程中仅仅依靠这些基本模块则还需要较多的工作。

幸运的是，Nodejs库和框架为我们提供了帮助，让我们减少工作量。

但是成百上千的库或者框架管理起来又很麻烦，有了NPM，可以很快的找到特定服务要使用的包，进行下载、安装以及管理已经安装的包。

**为什么要使用 npm？**

这里引用 **知乎** 上的一篇文章中的介绍：

------

当一个网站依赖的代码越来越多，程序员发现这是一件很麻烦的事情：

去 jQuery 官网下载 jQuery

去 BootStrap 官网下载 BootStrap

去 Underscore 官网下载 Underscore

...

有些程序员就受不鸟了，一个拥有三大美德的程序员 Isaac Z. Schlueter （以下简称 Isaaz）给出一个解决方案：用一个工具把这些代码集中到一起来管理吧！

这个工具就是他用 JavaScript （运行在 Node.js 上）写的 npm，全称是 Node Package Maganer

NPM 的思路大概是这样的：

1. 买个服务器作为代码仓库（registry），在里面放所有需要被共享的代码
2. 发邮件通知 jQuery、Bootstrap、Underscore 作者使用 npm publish 把代码提交到 registry 上，分别取名 jquery、bootstrap 和 underscore（注意大小写）
3. 社区里的其他人如果想使用这些代码，就把 jquery、bootstrap 和 underscore 写到 package.json 里，然后运行 npm install ，npm 就会帮他们下载代码
4. 下载完的代码出现在 node_modules 目录里，可以随意使用了。

这些可以被使用的代码被叫做「包」（package），这就是 NPM 名字的由来：Node Package(包) Manager(管理器)。

来源：知乎

作者：方应杭

链接：https://zhuanlan.zhihu.com/p/24357770

------

### gulp

简单来说，gulp 是一个前端工作流管理工具，你可以把他理解成一个可以帮你运行繁杂任务的工具，在本实验后面将使用gulp完成前端代码的整合编译

**gulp 中文网**

http://www.gulpjs.com.cn/

**中文文档**

http://www.gulpjs.com.cn/docs/

### laravel-elixir

**文档中对 laravel-elixir 的介绍**

Laravel Elixir 提供了简洁流畅的 API，让你能够在你的 Laravel 应用程序中定义基本的 Gulp 任务。Elixir 支持许多常见的 CSS 与 JavaScrtip 预处理器，甚至包含了测试工具。使用链式调用，Elixir 让你流畅地定义开发流程，例如：

```
elixir(function(mix) {
    mix.sass('app.scss')
       .coffee('app.coffee');
});
```

如果你曾经对于上手 Gulp 及编译资源文件感到困惑，那么你将会爱上 Laravel Elixir，不过 Laravel 并不强迫你使用 Elixir，你可以自由的选用你喜欢的自动化开发流程工具。

**laravel-elixir 文档**

https://laravel-china.org/docs/5.1/elixir

### sass

**百度百科对 sass 的介绍**

Sass 扩展了 CSS3，增加了规则、变量、混入、选择器、继承等等特性。Sass 生成良好格式化的 CSS 代码，易于组织和维护。

Sass 是对CSS3（层叠样式表）的语法的一种扩充，它可以使用巢状、混入、选择子继承等功能，可以更有效有弹性的写出Stylesheet。

Sass最后还是会编译出合法的CSS让浏览可以使用，也就是说它本身的语法并不太容易让浏览器识别（虽然它和CSS的语法非常的像，几乎一样），因为它不是标准的CSS格式，在它的语法内部可以使用动态变量等，所以它更像一种极简单的动态语言。

**sass 入门**

http://www.w3cplus.com/sassguide/

## 安装 bootstrap

因为 Laravel 默认集成了 bootstrap 所以安装也比较简单，Laravel 使用 nodejs 的包管理工具 npm 来对 bootstrap 进行集成

### package.json 文件介绍

package.json 文件是 npm 的配置文件，里边记录了项目用到的扩展包，每次执行 npm install 的时候，npm 就会根据 package.json 文件中的列表逐个安装扩展包，打开 laravel 目录下的 package.json 文件

```
{
  "private": true,
  "devDependencies": {
    "gulp": "^3.8.8"
  },
  "dependencies": {
    "laravel-elixir": "^4.0.0",
    "bootstrap-sass": "^3.0.0",
    "babel-preset-es2015": "~6.22.0",
    "babel-preset-react": "~6.22.0"
  }
}
```

如果你设置了`"private": true` , npm 就不会执行发布，这是为了防止无意中将私有库发布的情况

我们可以看到有 `devDependencies` 和 `dependencies` 两个对象

`devDependencies` 里面包含的是开发环境需要用到的扩展

`dependencies` 里面包含的是生产环境（应用运行）需要用到的扩展

比如我们的 gulp 是用来编译我们的 css js 等静态资源的，一旦编译好了放到服务器上就不需要再执行 gulp 了

也就是说服务器（生产环境）不需要 gulp 而我们的本机（开发环境）需要 gulp

所以 gulp 就放在了 `devDependencies` 里而不是 `dependencies` 里

`gulp` `laravel-elixir` `bootstrap-sass` 都是 Laravel 默认集成的

下面两个 babel 的组件是我为了修复一些bug加入的，可以忽略

### 安装

package.json　文件配置好后，就可以在根目录下执行　`npm install` 来安装全部的组件包

因为国内墙的原因 `npm install` 执行通常会非常慢，甚至卡死，所以一般使用 npm 国内镜像源加速，方法如下:

```
sudo npm install -g cnpm --registry=https://registry.npm.taobao.org
```

上面命令安装好 cnpm 以后，以后就可以直接使用 cnpm 命令来安装依赖了

```
cnpm install
```

但是即便使用国内镜像加速后，安装依赖包还是要耗费一些时间，所以**实验楼的在线环境已经为大家执行过了 npm install ，gulp 可以直接使用**

## 使用 bootstrap

在 Laravel 中使用 bootstrap 大概需要这么几个步骤

1. 安装 bootstrap 代码
2. 编写 sass 源文件
3. 编写 js 源文件
4. 配置 gulpfile.js
5. 编译生成 css 和 js 文件
6. 在html中引入 css 和 js 文件
7. 编写 bootstrap 风格的代码即可

现在我们已经完成了 bootstrap 代码的安装，如果你好奇 bootstrap 代码装在哪里了

可以打开代码根目录，找到 node_modules/bootstrap-sass 目录即可

### 编写 sass 源文件

打开代码根目录下的 resources/assets/sass/app.sass 文件

resources/assets/sass/app.sass

```
// @import "node_modules/bootstrap-sass/assets/stylesheets/bootstrap";
```

可以看到只有一行代码，这行代码的意思就是在该文件中引入 bootstrap ，但是默认是注释掉的，所以我们删掉注释保存

```
@import "node_modules/bootstrap-sass/assets/stylesheets/bootstrap";
```

当然了，除了引入 bootstrap 我们也可以在该文件中编写我们自己的样式代码，但是要符合 sass 的编码格式

### 编写 js 源文件

打开代码根目录下的 resources/assets 目录，创建一个 js 文件夹，然后在 js 文件夹中创建一个 app.js 并加入如下代码

resources/assets/js/app.js

```
window.$ = window.jQuery = require('jquery');
require('bootstrap-sass');
```

可以看到，在加载 bootstrap 之前，引入了 jquery ，因为 bootstrap 的 javascript 代码依赖 jquey 库

所以，我们还需要安装一下 jquery ，同样使用 npm 安装，在代码根目录下执行如下命令

```
npm install jquery --save
```

当然了，除了引入 bootstra 我们也可以在 app.js 文件中编写我们自己的 javascript 代码

### 配置 gulpfile.js

打开代码根目录下的 gulpfile.js 修改为如下代码

```
var elixir = require('laravel-elixir');

/*
 |--------------------------------------------------------------------------
 | Elixir Asset Management
 |--------------------------------------------------------------------------
 |
 | Elixir provides a clean, fluent API for defining some basic Gulp tasks
 | for your Laravel application. By default, we are compiling the Sass
 | file for our application, as well as publishing vendor resources.
 |
 */

elixir(function(mix) {
    mix.sass('app.scss')
       .browserify('app.js');
});
```

### 编译

sass 和 js 都编写完成之后，就可以执行编译了

在代码根目录下执行命令

```
gulp
```

你也可以执行

```
gulp --production
```

加上 `--production` 后缀，会将编译后的代码再进行压缩，使静态文件的体积最小化，一般在放到生产环境之前都要执行该命令，有兴趣的同学可以试一试，然后打开 public/css 或者 public/js 目录看一下生成的文件效果

如果 public 目录下 生成了 css 和 js 目录，说明编译成功

### 引用

最后一步就是在视图文件中引用编译生成的 css 和 js 文件

打开 resources/views/welcome.blade.php 文件，修改为如下代码

resources/views/welcome.blade.php

```
<!DOCTYPE html>
<html>
    <head>
        <title>Laravel</title>

        <!-- style -->
        <link rel="stylesheet" href="/css/app.css">
    </head>
    <body>
        <h1>Home</h1>

        <!-- js -->
        <script src="/js/app.js"></script>
    </body>
</html>
```

### 使用

现在 bootstrap 应该已经可以使用了，打开浏览器，访问 `http://localhost`

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2515timestamp1487154216950.png/wm)

其实现在 bootstrap 已经起作用了，因为看 home 的样式就看得出来是 bootstarp 对 h1 的样式

我们可以在浏览器上右键，然后选择查看页面源代码，来验证一下是不是已经成功加载了 bootstrap

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2515timestamp1487154636813.png/wm)

可以看到，CSS 和 JS 都成功加载了

现在就可以愉快的使用 bootstrap 了，我们可以打开 bootstarp 文档选择一些喜欢的样式

bootstarp 3 文档 ：http://v3.bootcss.com/

### 比如说我们想要一个导航栏

打开 bootstarp文档：导航栏：http://v3.bootcss.com/components/#navbar

直接粘贴代码到我们的网站中

resources/views/welcome.blade.php

```
<!DOCTYPE html>
<html>
    <head>
        <title>Laravel</title>

        <!-- style -->
        <link rel="stylesheet" href="/css/app.css">
    </head>
    <body>
        <nav class="navbar navbar-default">
          <div class="container-fluid">
            <!-- Brand and toggle get grouped for better mobile display -->
            <div class="navbar-header">
              <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1" aria-expanded="false">
                <span class="sr-only">Toggle navigation</span>
                <span class="icon-bar"></span>
                <span class="icon-bar"></span>
                <span class="icon-bar"></span>
              </button>
              <a class="navbar-brand" href="#">Brand</a>
            </div>

            <!-- Collect the nav links, forms, and other content for toggling -->
            <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">
              <ul class="nav navbar-nav">
                <li class="active"><a href="#">Link <span class="sr-only">(current)</span></a></li>
                <li><a href="#">Link</a></li>
                <li class="dropdown">
                  <a href="#" class="dropdown-toggle" data-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false">Dropdown <span class="caret"></span></a>
                  <ul class="dropdown-menu">
                    <li><a href="#">Action</a></li>
                    <li><a href="#">Another action</a></li>
                    <li><a href="#">Something else here</a></li>
                    <li role="separator" class="divider"></li>
                    <li><a href="#">Separated link</a></li>
                    <li role="separator" class="divider"></li>
                    <li><a href="#">One more separated link</a></li>
                  </ul>
                </li>
              </ul>
              <form class="navbar-form navbar-left">
                <div class="form-group">
                  <input type="text" class="form-control" placeholder="Search">
                </div>
                <button type="submit" class="btn btn-default">Submit</button>
              </form>
              <ul class="nav navbar-nav navbar-right">
                <li><a href="#">Link</a></li>
                <li class="dropdown">
                  <a href="#" class="dropdown-toggle" data-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false">Dropdown <span class="caret"></span></a>
                  <ul class="dropdown-menu">
                    <li><a href="#">Action</a></li>
                    <li><a href="#">Another action</a></li>
                    <li><a href="#">Something else here</a></li>
                    <li role="separator" class="divider"></li>
                    <li><a href="#">Separated link</a></li>
                  </ul>
                </li>
              </ul>
            </div><!-- /.navbar-collapse -->
          </div><!-- /.container-fluid -->
        </nav>

        <!-- js -->
        <script src="/js/app.js"></script>
    </body>
</html>
```

刷新页面，看一下效果

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2515timestamp1487155218724.png/wm)

是不是很简单？并且样式也还过得去，不算很丑

当然了，如果你对样式不满意，就只能在他的基础上进行一些修改，这就需要一些前端功底了

### 再举几个栗子

**比如想要一些按钮**

bootstarp文档：导航栏 http://v3.bootcss.com/css/#buttons

resources/views/welcome.blade.php

```
<!DOCTYPE html>
<html>
    <head>
        <title>Laravel</title>

        <!-- style -->
        <link rel="stylesheet" href="/css/app.css">
    </head>
    <body>
        .
        .
        .

        <!-- Standard button -->
        <button type="button" class="btn btn-default">Default</button>

        <!-- Provides extra visual weight and identifies the primary action in a set of buttons -->
        <button type="button" class="btn btn-primary">Primary</button>

        <!-- Indicates a successful or positive action -->
        <button type="button" class="btn btn-success">Success</button>

        <!-- Contextual button for informational alert messages -->
        <button type="button" class="btn btn-info">Info</button>

        <!-- Indicates caution should be taken with this action -->
        <button type="button" class="btn btn-warning">Warning</button>

        <!-- Indicates a dangerous or potentially negative action -->
        <button type="button" class="btn btn-danger">Danger</button>

        <!-- Deemphasize a button by making it look like a link while maintaining button behavior -->
        <button type="button" class="btn btn-link">Link</button>



        <!-- js -->
        <script src="/js/app.js"></script>
    </body>
</html>
```

效果图：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2515timestamp1487155581821.png/wm)

**比如想要一个登录表单**

bootstarp文档：水平排列的表单 http://v3.bootcss.com/css/#forms-horizontal

resources/views/welcome.blade.php

```
<!DOCTYPE html>
<html>
    <head>
        <title>Laravel</title>

        <!-- style -->
        <link rel="stylesheet" href="/css/app.css">
    </head>
    <body>
        .
        .
        .

        <form class="form-horizontal" style="margin-top:50px;">
          <div class="form-group">
            <label for="inputEmail3" class="col-sm-2 control-label">Email</label>
            <div class="col-sm-10">
              <input type="email" class="form-control" id="inputEmail3" placeholder="Email">
            </div>
          </div>
          <div class="form-group">
            <label for="inputPassword3" class="col-sm-2 control-label">Password</label>
            <div class="col-sm-10">
              <input type="password" class="form-control" id="inputPassword3" placeholder="Password">
            </div>
          </div>
          <div class="form-group">
            <div class="col-sm-offset-2 col-sm-10">
              <div class="checkbox">
                <label>
                  <input type="checkbox"> Remember me
                </label>
              </div>
            </div>
          </div>
          <div class="form-group">
            <div class="col-sm-offset-2 col-sm-10">
              <button type="submit" class="btn btn-default">Sign in</button>
            </div>
          </div>
        </form>



        <!-- js -->
        <script src="/js/app.js"></script>
    </body>
</html>
```

效果图：

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2515timestamp1487211734434.png/wm)

### bootstrap 的栅格布局

bootstarp 最经典的莫过于他的栅格布局设计了，它把一个屏幕分成了12列，通过这种栅格布局可以实现在任意屏幕上的自动适应效果

例如我们加入如下代码

resources/views/welcome.blade.php

```
<!DOCTYPE html>
<html>
    <head>
        <title>Laravel</title>

        <!-- style -->
        <link rel="stylesheet" href="/css/app.css">
    </head>
    <body>
        .
        .
        .


        <div class="container">
          <div class="row">
            <div class="col-md-4 col-sm-6" style="height:200px;background-color:red"></div>
            <div class="col-md-4 col-sm-6" style="height:200px;background-color:blue"></div>
            <div class="col-md-4 col-sm-6" style="height:200px;background-color:green"></div>
          </div>
        </div>


        <!-- js -->
        <script src="/js/app.js"></script>
    </body>
</html>
```

我使用 bootstarp 的栅格布局创建了三个空白 DIV 为了方便显示，给他们分别加上了背景色和固定的高度

当你缩小浏览器的窗口大小时，你可以看到排列方式会自动变化来适应不同的屏幕

![此处输入图片的描述](https://doc.shiyanlou.com/document-uid325811labid2515timestamp1487212412904.png/wm)