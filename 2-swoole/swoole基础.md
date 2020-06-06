# 一、Swoole基础

## 简介

### 1. Swoole是什么

- PHP异步网络通信引擎
- 最终编译为<kbd>.so文件</kbd>作为PHP的扩展
  
### 2. [特性](https://www.swoole.com/)
 - Swoole使用纯C语言编写，提供了PHP语言的<font color =red>**异步多线程**👍</font>服务器，**异步TCP/UDP网络**📶客户端，异步MySQL，异步Redis，数据库连接池，AsyncTask，消息队列，毫秒定时器，异步文件读写，异步DNS查询。Swoole内置 了Http/WebSocket服务器端/客户端、Http2.0服务器端。

 - 除了异步I/O的支持之外，Swoole 为PHP多进程的模式设计了多个并发数据结构和IPC通信机制，可以大大简化多进程并发编程的工作。其中包括了并发原子计数器，并发HashTable，Channel， Lock， 进程间通信IPC等丰富的功能特性。

 - Swoole2.0支持了类似Go语言的协程，可以使用完全同步的代码实现异步程序。PHP 代码无需额外增加任何关键词，底层自动进行协程调度，实现异步。

### 3.实际应用

- YY  
- 虎牙
- 战旗

## 准备工作

### 1. 环境

- linux环境下开发
- <font color=red>php7    swoole2.1    redis</font>
- <font color=red>源码安装php7   源码安装swoole</font>

### 2. PHP7源码编译安装

[php官网](https://www.php.net/downloads) 下载tag.bz2

1. **解压**  

   ```bash
   $ tar -xjvf (包名)
   ```

2. **configure预编译** <font color=red>检查安装环境</font>   需要GCC和pkg-config 以及 libxml2-dev

   ```bash
   sudo apt install gcc -y  &&
   sudo apt install g++ -y &&
   sudo apt install make -y &&
   sudo apt install openssl -y &&
   sudo apt install curl -y &&
   sudo apt install libbz2-dev -y &&
   sudo apt install libxml2-dev -y &&
   sudo apt install libjpeg-dev -y &&
   sudo apt install libpng-dev -y &&
   sudo apt install libfreetype6-dev -y &&
   sudo apt install libzip-dev -y &&
   sudo apt install libssl-dev -y &&
   sudo apt install libsqlite3-dev -y &&
   sudo apt install libcurl4-openssl-dev -y &&
   sudo apt install libgmp3-dev -y &&
   sudo apt install libonig-dev -y &&
   sudo apt install libreadline-dev -y &&
   sudo apt install libxslt1-dev -y &&
   sudo apt install libffi-dev -y &&
   sudo apt install build-essential -y
   ```
   
   ```shell
   $ yum install libxml2 libxml2-devel openssl openssl-devel bzip2 bzip2-devel libcurl libcurl-devel libjpeg libjpeg-devel libpng libpng-devel freetype freetype-devel gmp gmp-devel libmcrypt libmcrypt-devel readline readline-devel libxslt libxslt-devel
    sqlite-devel
   ```
   
   
   
   ```bash
   $ ./configure --help #查看命令
   $ ./configure --prefix=/home/soft/php \
   --enable-fpm --with-mysql \
   --with-mysqli --with-pdo-mysql \
   --with-openssl --enable-debug \
   --with-curl#指定安装路径
   ```
   
3. **make **编译构建

4. **make install** 安装

   ```shell
   $ make -j && make install
   ```

5. 配置PHP**环境PATH**

   ```bash
   $ alias php=/home/kiyo/study/soft/php/bin/php
   ```

   或者

   ```shell
   $ vi ~/.profile
   
   #修改以下路径
   #export PATH
   #alias php=/home/kiyo/study/soft/php/bin
   
   $ source ~/.bash_profile
   $ php -v
   ```

6. 自定义路径下**配置文件** 解压文件目录下有：**php.ini**-development php.ini-production 

   ```shell
   $ cp php.ini-development  /home/name/soft/php/bin/php/etc #注意目录位置
   $ mv php.ini-development php-ini #改名
   $ php -i |grep php.ini #查看文件位置
   $ mv ./etc/php.ini ./lib/ #根据配置，可能需要移动位置
   $ cp ./etc/php-fpm.d/www.conf.default ./etc/php-fpm.d/www.conf
   ```




### 3. 安装Swoole

1. [github上](https://github.com/swoole/swoole-src) 克隆  或者  下载zip并解压 unzip

   ```shell
   $ git clone https://github.com/swoole/swoole-src.git
   ```

2. 使用***phpize***生成configure文件  编译安装

   ```shell
   $ /home/kiyo/study/soft/php/bin/phpize #在该路径下生成
   $ ./configure --with-php-config=/home/kiyo/study/soft/php/bin/php-config
   $ make -j && make install
   #进入安装目录可以发现swoole.so
   Installing shared extensions:     /home/kiyo/study/soft/php/lib/php/extensions/no-debug-non-zts-20190902/            Installing header files:          /home/kiyo/study/soft/php/include/php/ 
   ```

3. 源码目录下试用Demo    /swoole-src-master/examples

   ```shell
   $ php echo.php #发现报错
   
   $ php -m # 查看模块
   
   $ vi /php/lib/php.ini #修改文件 加入如下字段
   extension=swoole
   
   $ php -m # 查看模块
   [PHP Modules]                                                       swoole 
   $ netstat -anp|grep 9501 #查看端口占用 wsl与windows共用端口
   $ netstat -anp|findstr 9501 #cmd中使用
   ```

   到此已经正确启用swoole拓展

### 4. nginx 源码安装

[nginx官网](https://nginx.org/en/download.html)下载安装包解压

[安装方法](https://www.php.net/manual/zh/install.unix.nginx.php)

$ ./configure --prefix=/home/kiyo/study/soft/nginx #指定安装路径

### 5. yum版安装

- ###### nginx的安装

  [yum源修改](https://www.imooc.com/video/18203)

  ```bash
  $ man yum #查看说明
  $ cd /etc/yum.repos.d/ #修改yum源
  
  $ yum search nginx
  ```

  创建nginx的rep(https://nginx.org/en/linux_packages.html)

  ```bash
  $ sudo yum install nginx #安装
  
  $ whereis nginx #查看位置
  
  $ nginx #启动
  ```

  阿里云需要配置安全组规则才能看到页面

- php的安装

  ```bash
  $ rpm -qa | grep php
  $ yum remove php* #删除所有php相关包
  
  #添加源
  $ rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm  
  $ rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm  
  
  $ yum search php7
  ```

  

## Swoole网络通信引擎的使用

### 1.TCP服务器



- 创建服务器

    创建了一个 `TCP` 服务器，监听本机 `9501` 端口。它的逻辑很简单，当客户端 `Socket` 通过网络发送一个 `hello` 字符串时，服务器会回复一个 `Server: hello` 字符串。

    

    `Server` 是**异步服务器**，所以是通过监听事件的方式来编写程序的。当对应的事件发生时底层会主动回调指定的函数。如当有新的 `TCP` 连接进入时会执行 [onConnect](https://wiki.swoole.com/#/server/events?id=onconnect) 事件回调，当某个连接向服务器发送数据时会回调 [onReceive](https://wiki.swoole.com/#/server/events?id=onreceive) 函数。

    

    - `$fd` 就是客户端连接的唯一标识符，服务器可以同时被成千上万个客户端连接
    - 调用 `$server->send()` 方法向客户端连接发送数据，参数就是 `$fd` 客户端标识符
    - 调用 `$server->close()` 方法可以强制关闭某个客户端连接
    - 客户端可能会主动断开连接，此时会触发 [onClose](https://wiki.swoole.com/#/server/events?id=onclose) 事件回调

    ```php
    //创建Server对象，监听 127.0.0.1:9501端口
    $serv = new Swoole\Server("127.0.0.1", 9501); 
    
    //设置运行时的各项参数
    $serv->set(array(
        'reactor_num'   => 2,     // reactor thread num
        'worker_num'    => 4,     // worker进程数目
        'backlog'       => 128,   // listen backlog
        'max_request'   => 50,
        'dispatch_mode' => 1,
    ));
    //监听连接进入事件
    $serv->on('Connect', function ($serv, $fd, $reactor_id) {  
        echo "Client: Connect.\n";
    });
    
    //监听数据接收事件
    $serv->on('Receive', function ($serv, $fd, $from_id, $data) {
        $serv->send($fd, "Server: ".$data);//向客户端连接发送数据
    });
    
    //监听连接关闭事件 客户端主动断开连接
    $serv->on('Close', function ($serv, $fd) {
        echo "Client: Close.\n";
    });
    
    //启动服务器
    $serv->start(); 
    ```


- 执行程序 连接该端口

  在命令行下运行 `server.php` 程序，启动成功后可以使用 `netstat` 工具看到，已经在监听 `9501` 端口。这时就可以使用 `telnet/netcat` 工具连接服务器。

  ```shell
  $ telnet 127.0.0.1 9501 #连接端口
  $ hello		#客户端发送
  $ Server: hello #服务器返回
  ```

  <kbd>ctrl</kbd>+<kbd>]</kbd> 打quit退出 telnet工具

  

- 查看进程数目

  `ps aft |grep tcp_server.php`

- 示意图

  ![image-20200605172219279.png](https://i.loli.net/2020/06/05/eBWXis1xIdJZDYk.png)

### 2.TCP客户端

- 建立客户端

  ```php
  $client = new Swoole\Client(SWOOLE_SOCK_TCP);
  
  if (!$client->connect('127.0.0.1', 9501, 0.5)){//连接服务器
      echo "connect failed. Error: {$client->errCode}\n";
  }
  //php lic常量
  fwrite(STDOUT,'请输入需要发送的消息:');
  $msg = trim(fgets(STDIN));
  $client->send($msg);//发送数据
  
  echo $client->recv();//接收数据
  
  $client->close();//关闭客户端
  ```

### 3.UDP服务器/客户端

- 与TCP基本相同，仅实例化时修改参数

```php
$client = new Swoole\Client(SWOOLE_SOCK_UDP);
```

---

### 4.HTTP服务器

- 示意图 

  <img src="https://i.loli.net/2020/06/05/mLTW1kgpJPbQ8q2.png" alt="image-20200605182718429" style="zoom:50%;" />

  swoole可以将其作为应用服务器，nginx使用`反向代理`作为Web服务器的前置机来降低网络和服务器的负载，提高访问效率

  

- 创建服务器

  **onRequest 事件**

  在收到一个完整的 HTTP 请求后，会回调此函数。回调函数共有 `2` 个参数：

  - [$request](https://wiki.swoole.com/#/http_server?id=httprequest)，`HTTP` 请求信息对象，包含了 `header/get/post/cookie` 等相关信息
  - [$response](https://wiki.swoole.com/#/http_server?id=httpresponse)，`HTTP` 响应对象，支持 `cookie/header/status` 等 `HTTP` 操作
  - 在 [onRequest](https://wiki.swoole.com/#/http_server?id=on) 回调函数返回时底层会销毁 `$request` 和 `$response` 对象

  ```php
  $http = new Swoole\Http\Server("0.0.0.0", 2233);
  //设置静态文件访问目录
  $http->set([
      'document_root' => '/home/soft/nginx/html/study/swoole',
      'enable_static_handler' => true
  ]);
  $http->on('request', function ($request, $response) {
      $response->end("<h1>Hello Swoole. #".rand(1000, 9999)."</h1>");
  });
  $http->start();
  ```

- 测试

  ```shell
  $ curl 127.0.0.1:2233
  ```

- request对象常用内容

  | 对象包含内容          | request->              |
  | --------------------- | :--------------------- |
  | **头部信息**          | header['host']         |
  | **服务器信息**        | server['request_time'] |
  | **请求的 `GET` 参数** | get['orderid']         |
  | **`POST` 参数**       | post['get['orderid']   |
  | **`COOKIE` 信息**     | cookie['username']     |



### 5.WebSocket服务

WebSocket协议是基于TCP的一种新的网络协议。它实现了浏览器与服务器全双工(full-duplex)通信一允许`服务器主动发送信息给客户端`。HTTP的通信只能由客户端发起

- WebSocket特点

  - 建立在`TCP协议`之上
  - `性能开销小通信高效`
  - 客户端可以与任意服务器通信
  - 协议标识符ws wss
  - `持久化`网络通信协议 `长连接`

  ```php
  <?php
  $server = new Swoole\WebSocket\Server("0.0.0.0", 2233);
  
  
  $server->on('open', function (Swoole\WebSocket\Server $server, $request) {
      echo "server: handshake success with fd{$request->fd}\n";
  });
  //监听ws消息事件
  $server->on('message', function (Swoole\WebSocket\Server $server, $frame) {
      echo "receive from {$frame->fd}:{$frame->data},opcode:{$frame->opcode},fin:{$frame->finish}\n";
      $server->push($frame->fd, "this is server");
  });
  //关闭事件
  $server->on('close', function ($ser, $fd) {
      echo "client {$fd} closed\n";
  });
  
  $server->start();
  ```

- 面向对象风格

  ```php
  class WebSocketTest
  {
      public $server;
  
      public function onOpen($server, $request) {
          echo "server: handshake success with fd{$request->fd}\n";
      }
      public function onMessage($server, $frame) {
          echo "receive from {$frame->fd}:{$frame->data},opcode:{$frame->opcode},fin:{$frame->finish}\n";
          $server->push($frame->fd, "this is server");
      }
      public function onClose($ser, $fd) {
          echo "client {$fd} closed\n";
      }
      public function __construct()
      {
          $this->server = new Swoole\WebSocket\Server("0.0.0.0", 2233);
          $this->server->on('open', [$this, 'onOpen']);
          $this->server->on('message', [$this,'onMessage']);
          $this->server->on('close', [$this,'onClose']);
          $this->server->start();
      }
  }
  
  new WebSocketTest();
  ```

- 客户端使用http服务器

  ```php
  $http = new Swoole\Http\Server("0.0.0.0", 2234);
  $http->set([
      'document_root' => '/home/soft/nginx/html/study/swoole',
      'enable_static_handler' => true
  ]);
  $http->on('request', function ($request, $response) {
      $response->end("<h1>Hello Swoole. #".rand(1000, 9999)."</h1>");
  });
  $http->start();
  ```

  html代码

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <title>Title</title>
  </head>
  <body>
  <h1>test</h1>
      <script>
          var wsUrl = 'ws://60.205.154.9:2233/';
          var websocket = new WebSocket(wsUrl);
          //实例对象的onopen属性
          websocket.onopen = function(evt) {
            websocket.send("hello-swoole");
            console.log("conected-swoole-success");
          }
  
          // 实例化 onmessage
          websocket.onmessage = function(evt) {
            console.log("ws-server-return-data:" + evt.data);
          }
  
          //onclose
          websocket.onclose = function(evt) {
            console.log("close");
          }
          //onerror
  
          websocket.onerror = function(evt, e) {
            console.log("error:" + evt.data);
          }
      </script>
  </body>
  </html>
  ```

  

### 6. Task任务使用

- 执行异步任务 (Task)

  在 Server 程序中如果需要执行很`耗时`的操作，比如一个聊天服务器发送广播，Web 服务器中发送邮件。如果直接去执行这些函数就会`阻塞当前进程`，导致服务器响应变慢。

  Swoole 提供了异步任务处理的功能，可以投递一个异步任务到 `TaskWorker 进程池`中执行，不影响当前请求的处理速度。

  基于第一个 TCP 服务器，只需要增加 [onTask](https://wiki.swoole.com/#/server/events?id=ontask) 和 [onFinish](https://wiki.swoole.com/#/server/events?id=onfinish) 2 个事件回调函数即可。另外需要设置 task 进程数量，可以根据任务的耗时和任务量配置适量的 task 进程

```php
$this->server->set(
    [
        'worker_num' => 2,
        'task_worker_num' => 2,
    ]
);
$this->server->on('task', [$this,'onTask']);
$this->server->on("finish", [$this, 'onFinish']);

public function onMessage($server, $frame) {
    echo "receive from {$frame->fd}:{$frame->data},opcode:{$frame->opcode},
        fin:{$frame->finish}\n";
    $data = [
        'task'=>1,
        'fd'=>$frame->fd,
    ];
    $server->task($data);//发送消息中测试
    $server->push($frame->fd, "server push:".date("Y-m-d H:i:s"));
}

public function onTask($ser, $taskId, $workerId, $data){
    print_r($data);
    //模拟耗时场景
    sleep(10);
    return "on task finish";
}
public function onFinish($ser, $taskId, $data){
    echo 'taskId:'.$taskId."\n";
    echo 'finish-data-success:'.$data."\n";
}
```

---

## 异步非堵塞IO场景

### Swoole毫秒定时器

- ### [tick()](https://wiki.swoole.com/#/timer?id=tick)

  设置一个间隔时钟定时器。

  与 `after` 定时器不同的是 `tick` 定时器会**持续触发**，直到调用 [Timer::clear](https://wiki.swoole.com/#/timer?id=clear) 清除。

  ```php
  Swoole\Timer::tick(int $msec, callable $callback_function, ...$params): int;
  ```

  

- ### [after()](https://wiki.swoole.com/#/timer?id=after)

  在指定的时间后执行函数。`Swoole\Timer::after` 函数是一个一次性定时器，执行完成后就会销毁。

  此函数与 `PHP` 标准库提供的 `sleep` 函数不同，`after` 是非阻塞的。而 `sleep` 调用后会导致当前的进程进入阻塞，将无法处理新的请求。

  ```php
  Swoole\Timer::after(int $msec, callable $callback_function, ...$params): int;
  ```

  

### 异步文件系统I/O

- ### [readFile()](https://wiki.swoole.com/#/coroutine/system?id=readfile)协程方式读取文件

  ```php
  Swoole\Coroutine\System::readFile(string $filename): string|false;
  ```

- ### [writeFile()](https://wiki.swoole.com/#/coroutine/system?id=writefile)协程方式写入文件

  ```php
  Swoole\Coroutine\System::writeFile(string $filename, string $fileContent, int $flags): bool;
  ```

- 存储日志

  ```php
  $content = [
          'date:' => date("Ymd H:i:s"),
          'get:' => $request->get,
          'post:' => $request->post,
          'header:' => $request->header,
      ];
      $filename = __DIR__ . "/access.log";
  	
      $w = Swoole\Coroutine\System::writeFile($filename, json_encode($content),$flags=FILE_APPEND);//在文件末尾追加
      var_dump($w);
  ```

- 动态打印日志

  ```shell
  $ tail -f access.log
  ```

  

### 异步Mysql详解

​		

### 异步Redis 

## 进程管理、内存、协程

